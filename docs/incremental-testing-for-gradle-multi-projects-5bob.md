# 梯度多项目的增量测试

> 原文：<https://dev.to/patxibocos/incremental-testing-for-gradle-multi-projects-5bob>

[![time](img/e5ae9fb3a4e59910c4e5db0d5e4f65a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QDI_ep02--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.tracksmart.com/wp-content/uploads/2016/07/Employee-Time-Theft-at-Work-Robbing-Your-Business-LG.jpg.jpg) 
应用模块化在 Android 上是相当普遍的事情，甚至 Google 在上一期 [I/O](https://www.youtube.com/watch?v=PZBg5DIzNww) 中也谈到了。无论您只是使用它来分隔不同的领域，还是实现一个具有多个层次的清晰架构，您最终都会得到多个梯度子项目。模块化应用的好处之一是加快构建时间，但你可以走得更远。

每个子项目可能会有一些测试(希望如此)，这些测试将在您的本地机器和一些 CI 系统上运行。当有人在项目的 Git 存储库上提交 PR 时，一些工作流将被触发来运行测试、测量覆盖率、运行 linters 等。如果你的应用程序是基于一个大的代码库，并且有几个测试，这可能需要很长时间。我们能做些什么来避免每次都运行所有的测试吗？也许一个 PR 正在修改一个子项目中的单个类，我们不需要在每个子项目上运行测试。

为了改进这一点，我们将构建一个 Gradle 任务，它执行以下操作:

*   与另一个分支相比，检测在某个分支中添加/修改/删除了哪些文件(基本)。
*   确定这些文件属于哪个子项目。
*   对于每个子项目，每隔一个子项目递归遍历一次，以收集哪些子项目依赖于它们。
*   为所有受影响的项目运行测试任务。

# 在分支机构之间改变了文件

这基本上包括做一个`git diff`。为了运行这个，我们得到了来自 [JGit](https://www.eclipse.org/jgit/) 库:
的支持

```
implementation("org.eclipse.jgit:org.eclipse.jgit:5.4.0.201906121030-r") 
```

给定一些 Gradle 项目对象和一个分支名称，我们可以构建一些 JGit 的 Git 实例对象:

```
val git: Git = Git.open(File(rootProject.projectDir.path)) 
```

该对象包含一个返回一个`DiffCommand`的`diff()`函数，需要设置它来比较新旧树。这是通过分别调用`setOldTree`和`setNewTree`函数完成的。这些函数需要一个`AbstractTreeIterator`，我们必须如下构建:

```
private fun getGitReferenceTree(repository: Repository, ref: String): AbstractTreeIterator {
    val head = repository.exactRef(ref)
    RevWalk(repository).use { walk ->
        val commit = walk.parseCommit(head.objectId)
        val tree = walk.parseTree(commit.tree.id)
        val treeParser = CanonicalTreeParser()
        repository.newObjectReader().use { reader -> treeParser.reset(reader, tree.id) }
        walk.dispose()
        return treeParser
    }
} 
```

这段代码改编自 jgit 食谱。对于给定的存储库和 Git ref，它将解析 ref 以获取头部，然后检索 commit 和 tree 以最终构建一个指向分支的 CanonicalTreeParses。

```
internal fun getDiffFilePaths(rootProject: Project, branchName: String): Iterable<String> {
    val git = Git.open(File(rootProject.projectDir.path))
    val masterTree = getGitReferenceTree(git.repository, "refs/remotes/origin/master")
    val branchTree = getGitReferenceTree(git.repository, "refs/remotes/origin/$branchName")
    val diffEntries = git.diff().setOldTree(masterTree).setNewTree(branchTree).call()
    return diffEntries.flatMap { diffEntry -> listOf(diffEntry.oldPath, diffEntry.newPath) }
} 
```

上面的方法将为每个条目返回旧的和新的路径，因此如果一个文件从子项目 A 移动到子项目 B，那么相对于每个子项目的两个路径都将被检索。

# 受不同文件路径影响的分级子项目

给定文件列表的相对路径，很容易得到这些文件所属的 Gradle 项目。我们只需要过滤掉那些`projectDir`包含在任何一个文件路径开头的项目:

```
private fun getProjectsForFilePaths(rootProject: Project, filePaths: Iterable<String>): Iterable<Project> =
    rootProject.subprojects.filter { subproject ->
        filePaths.find { filePath ->
            filePath.startsWith(subproject.projectDir.toRelativeString(rootProject.projectDir))
        } != null
    } 
```

# 计算依赖项目

由于这些项目中的任何一个都可能是任何其他项目的依赖项，我们正在查找可能依赖于这些项目中的任何一个的其他项目(递归地)。

对于一个给定的项目，以及一个所有项目的列表，我们将计算依赖项，这些依赖项在配置 api 或实现下过滤包含给定项目的类型项目的依赖项:

```
private fun getDependantProjects(allProjects: Iterable<Project>, dependencyProject: Project): Iterable<Project> {
    val dependants = allProjects.filter { subproject ->
        try {
            val implementationDependencies = subproject.configurations.getByName("implementation").dependencies
            val apiDependencies = subproject.configurations.getByName("api").dependencies
            (implementationDependencies + apiDependencies).filterIsInstance<DefaultProjectDependency>().find { dependency ->
                dependency.dependencyProject == dependencyProject
            } != null
        } catch (exception: UnknownConfigurationException) {
            false
        }
    }
    return dependants + dependants.flatMap { getDependantProjects(allProjects, it) }
} 
```

这将返回所有直接依赖于给定项目的项目。要实现这种递归，我们只需要调用函数并展平结果:

```
return dependants + dependants.flatMap {
    getDependantProjects(allProjects, it)
} 
```

结合我们之前的函数，结果将是:

```
fun projectsAffectedByBranch(branchName: String, rootProject: Project): Collection<Project> {
    val diffFilePaths = getDiffFilePaths(rootProject, branchName)
    val affectedProjects = getProjectsForFilePaths(rootProject, diffFilePaths)
    return affectedProjects.flatMap { getDependantProjects(rootProject.subprojects, it) + it }.distinct()
} 
```

我们再次扁平化，因为调用 getDependantProjects 的结果也返回一个列表。调用`distinct()`是必要的，因为我们不想要重复的。

# 对受影响的项目运行测试任务

一旦我们有了受影响项目的列表，我们只需要寻找类型测试的任务，并依靠它们来运行:

```
tasks.register("testsForBranch") {
    val branchProperty = "branch"
    if (!project.hasProperty(branchProperty)) {
        println("please specify branch property with -P$branchProperty=<$branchProperty>")
        return@register
    }
    val branchName = project.property(branchProperty).toString()
    val affectedProjects = projectsAffectedByBranch(branchName, rootProject)
    affectedProjects.forEach {
        val testTasks = it.tasks.withType(Test::class.java).filter {
            it.name in arrayOf("test", "testDebugUnitTest")
        }
        dependsOn(testTasks)
    }
} 
```

在这个特殊的例子中，我们过滤测试任务只是为了找到那两个。

为了对名为 test 的分支运行这个任务，我们将运行这个命令:

```
./gradlew testsForBranch -Pbranch=test 
```

编码快乐！✌
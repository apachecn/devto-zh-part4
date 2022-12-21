# 自动 umbracoSettings.config 修改

> 原文：<https://dev.to/akuryan/automated-umbracosettings-config-modifications-56h9>

这是从我的博客交叉发布的。

这是节省时间并使构建工程师的生活更容易的自动化难题的又一部分。本部分旨在消除重复工作，这些工作是在 Umbraco 驱动的连续交付(CD)项目的团队级配置中已经完成的。

Umbraco 是一个开源的 ASP.NET CMS，有一个很大的开发者社区。这个 CMS 的一个特性是一个配置文件系统，它由 web.config 和其他一些文件组成，这使得自动交付对于构建工程师来说更难配置。配置文件，存储在~/config 目录中。这是一个绝妙的想法——每个 Umbraco 部件都由自己的配置文件驱动，但是当您必须在这些文件中设置一些特定于环境的设置时，这就成了一个噩梦。即使在 Visual Studio 2015 中，配置文件转换也只允许您构建 web.config 文件转换。当然，SlowCheetah 插件缓解了这个问题，但是它给构建工程师带来了额外的工作。因为我们已经建立了自动化的连续交付过程，所以我们的主要目标是移除所有那些分散注意力的小配置，并且自动化所有 j。

在这篇文章中，我将介绍对 umbracoSettings.config 文件的两个部分中的一个进行自动修改，这两个部分分别负责在 Umbraco 中保持激活、任务调度和延迟发布。

让我非常难过的是，Umbraco 有两个这样的参数，它们是依赖于版本的:

1.  对于 6.2.5 和 7.1.9-7.2.7 版，它是 scheduledTasks 元素的 baseUrl 属性(自 7.2.7 版以来，它已过时，在以前的版本中不存在)

2.  从 7.2.7 开始，它是 web.routing 元素的 umbracoApplicationUrl 属性

两者的目标:从关于 Umbraco backoffice 访问 URL 的第一个请求中删除自动猜测，并通过配置对其进行设置(在[https://our . um braco . org/documentation/reference/config/um braco settings/](https://our.umbraco.org/documentation/reference/config/umbracosettings/)和【http://issues.umbraco.org/issue/U4-6788】T2 中有详细介绍)。

## 代码及解释

首先，我们必须修改 MsBuild 编译过程，通过将它添加到集中式项目文件 PropertyGroup 元素:
来确保该任务在编译之后和配置文件转换之前直接执行

```
<PropertyGroup>
    <TargetsTriggeredByCompilation>
        $(TargetsTriggeredByCompilation);
        UmbracoSettingsConfigTransform; 
    </TargetsTriggeredByCompilation>    
</PropertyGroup> 
```

这将确保我们的目标 UmbracoSettingsConfigTransform 在编译后被直接调用。其次，由于需要灵活性，我们在同一个 PropertyGroup 中添加了一些变量，允许停止自动转换:

```
<PropertyGroup>
    <!-- If system.DoNotSetScheduledTasksBaseUrl is not defined - it shall be equal to false -->
    <DoNotSetScheduledTasksBaseUrl Condition="'$(DoNotSetScheduledTasksBaseUrl)'==''">False</DoNotSetScheduledTasksBaseUrl>
    <!--
As seen by name starting with underscores - __SetScheduledTasksBaseUrl - is internal variable.
It's target - to establish setup of scheduled task base url
-->
    <__SetScheduledTasksBaseUrl>!$(DoNotSetScheduledTasksBaseUrl)</__SetScheduledTasksBaseUrl>
    <!-- If system.DoNotSetUmbracoApplicationUrl is not defined - it shall be equal to false -->
    <DoNotSetUmbracoApplicationUrl Condition="'$(DoNotSetUmbracoApplicationUrl)'==''">False</DoNotSetUmbracoApplicationUrl>
    <__SetUmbracoApplicationUrl>!$(DoNotSetUmbracoApplicationUrl)</__SetUmbracoApplicationUrl>
</PropertyGroup> 
```

可以看出，如果没有设置变量 DoNotSetScheduledTasksBaseUrl 和 DoNotSetUmbracoApplicationUrl，它们将被设为 False，从而允许进一步的转换。如果设置了变量，构建过程将接收相应的值。之后，构建过程会将原始的、未修改的版本复制到临时存储中。构建完成后，未修改的版本将被放回，以缓解可能的版本控制系统(VCS)签出问题:

```
<Target Name="CopySourceTransformFiles" BeforeTargets="Cleanup" >
    <Copy SourceFiles="$(UmbracoSettingsConfigFullPath)" DestinationFolder="$(IntermediateOutputPath)" OverwriteReadOnlyFiles="True" />
</Target>
<Target Name="RestoreSourceTransformFiles" BeforeTargets="Cleanup" >
    <Copy SourceFiles="$(IntermediateOutputPath)$(UmbracoSettingsConfigName)" DestinationFolder="$(umbracoConfigFolder)" Condition="$(__SetScheduledTasksBaseUrl) Or $(__SetUmbracoApplicationUrl)" />
</Target> 
```

接下来，我们必须检查 2 个可能的修改中是否有一个对当前的 Umbraco 版本有效。Umbraco 将当前安装的版本存储在 web.config 的 appSettings 部分中的一个注册表项 umbracoConfigurationStatus 的值中。因此，在构建时，代码使用 XmlPeek MsBuild 任务检索该值，然后使用 Nuget 包语义版本化([https://www.nuget.org/packages/semanticversioning/открывается](https://www.nuget.org/packages/SemanticVersioning/%D0%9E%D1%82%D0%BA%D1%80%D1%8B%D0%B2%D0%B0%D0%B5%D1%82%D1%81%D1%8F)вномокне，[https://github.com/adamreeve/semver.netОткрывается](https://github.com/adamreeve/semver.net%D0%9E%D1%82%D0%BA%D1%80%D1%8B%D0%B2%D0%B0%D0%B5%D1%82%D1%81%D1%8F)вновомокне)版本与使用自定义 MsBuild 任务的范围进行比较，内部变量接收真值或假值(允许或不允许修改)

```
<UsingTask TaskName="CompareSemanticVersions" AssemblyFile="$(TeamCityCiToolsPath)\CI.Builds\MsBuildCustomTasks\Colours.Ci.MSBuild.CustomTasks.dll"/>
  <Target Name="EstablishConfigFileModificationsRequirements" Condition="$(__umbraco_config_setUmbracoUrl)">
    <!-- Get version from web.config -->
    <XmlPeek Namespaces="&lt;Namespace Prefix='msb' Uri='http://schemas.microsoft.com/developer/msbuild/2003'/&gt;"
          XmlInputPath="web.config"
          Query="configuration/appSettings/add[@key = 'umbracoConfigurationStatus']/@value"
          Condition="Exists('web.config')">
      <Output TaskParameter="Result" PropertyName="__umbracoVersionFromWebConfig" />
    </XmlPeek>
    <Message Text="Umbraco version peeked from web.config: $(__umbracoVersionFromWebConfig)" Importance="high" />
    <!--
            Now we have to check, which Umbraco version we are working with.
            baseUrl for scheduled tasks are allowed in 6.2.5 and 7.1.9 - 7.2.7
            If version does not fit in this range - baseUrl shall not be set.
            If version is 7.2.7 and higher - web.routing attribute umbracoApplicationUrl shall be used
        -->
    <CompareSemanticVersions CurrentVersion="$(__umbracoVersionFromWebConfig)" AllowedVersionRange="6.2.5 || &gt;=7.1.9 &lt;7.2.7">
      <Output TaskParameter="VersionIsInRange" PropertyName="__SetScheduledTasksBaseUrl"/>
    </CompareSemanticVersions>
    <Message Text="We are going to set scheduledTasks baseUrl" Importance="high" Condition="$(__SetScheduledTasksBaseUrl)"/>
    <Message Text="We are NOT going to set scheduledTasks baseUrl" Importance="high" Condition="!$(__SetScheduledTasksBaseUrl)"/>
    <MSBuild Projects="$(MSBuildProjectFile)" Properties="CustomConfigFileToTransfom=$(_PackageTempDir)\$(UmbracoSettingsConfigFullPath);CustomConfigTransformFile=$(TeamCityCiToolsPath)\CI.Builds\SharedFiles\Umbraco\umbraco.umbracosettings.scheduledurl.config.transform" Targets="TransformCustomConfigFile" Condition="$(__SetScheduledTasksBaseUrl) AND Exists('$(_PackageTempDir)\$(UmbracoSettingsConfigFullPath)')" />
    <CompareSemanticVersions CurrentVersion="$(__umbracoVersionFromWebConfig)" AllowedVersionRange="&gt;=7.2.7" >
      <Output TaskParameter="VersionIsInRange" PropertyName="__SetUmbracoApplicationUrl"/>
    </CompareSemanticVersions>
    <Message Text="We are going to set web.routing umbracoApplicationUrl" Importance="high" Condition="$(__SetUmbracoApplicationUrl)"/>
    <Message Text="We are NOT going to set web.routing umbracoApplicationUrl" Importance="high" Condition="!$(__SetUmbracoApplicationUrl)"/>
    <MSBuild Projects="$(MSBuildProjectFile)" Properties="CustomConfigFileToTransfom=$(_PackageTempDir)\$(UmbracoSettingsConfigFullPath);CustomConfigTransformFile=$(TeamCityCiToolsPath)\CI.Builds\SharedFiles\Umbraco\umbraco.umbracosettings.umbracoApplicationUrl.config.transform" Targets="TransformCustomConfigFile" Condition="$(__SetUmbracoApplicationUrl) AND Exists('$(_PackageTempDir)\$(UmbracoSettingsConfigFullPath)')" />
  </Target> 
```

我希望专注于自定义任务:

```
<CompareSemanticVersions
CurrentVersion="$(__umbracoVersionFromWebConfig)"
AllowedVersionRange="6.2.5 || &gt;=7.1.9 &lt;7.2.7"
Condition="$(__SetScheduledTasksBaseUrl)">
<Output TaskParameter="VersionIsInRange" PropertyName="__SetScheduledTasksBaseUrl"/>
</CompareSemanticVersions> 
```

这个任务的代码很简单:

```
namespace Colours.Ci.MSBuild.CustomTasks.HelperMethods.StringMethods
{
    using System;

    using Loging;

    using Microsoft.Build.Framework;
    using Microsoft.Build.Utilities;

    using SemVer;

    using Version = SemVer.Version;

    public class CompareSemanticVersions : Task
    {
        /// <summary>
        /// Current version of software
        /// </summary>
        [Required]
        public string CurrentVersion { get; set; }
        /// <summary>
        /// Allowed versions range of software.
        /// Shall use ranges, as described https://github.com/npm/node-semver#ranges
        /// Also, see https://github.com/adamreeve/semver.net#ranges
        /// </summary>
        [Required]
        public string AllowedVersionRange { get; set; }
        /// <summary>
        /// Demonstrates, if version is in range
        /// </summary>
        [Output]
        public bool VersionIsInRange { get; private set; }

        public override bool Execute()
        {
            VersionIsInRange = CheckVersions(CurrentVersion, AllowedVersionRange);
            return true;
        }

        public static bool CheckVersions(string currentVersion, string allowedVersionRange)
        {
            var logWriter = new Logging();
            Version version;
            try
            {
                version = new Version(currentVersion);
            }
            catch (Exception exception)
            {
                logWriter.LogWriter(string.Concat("There was an error ", exception.Message, "while parsing ", currentVersion));
                return false;
            }

            var range = new Range(allowedVersionRange);
            return range.IsSatisfied(version);
        }
    }
} 
```

实际上，它只是一个代码，编译成汇编。它接收 current version 参数中的当前版本，NPM 符号([https://github.com/npm/node-semver#ranges](https://github.com/npm/node-semver#ranges))中 AllowedVersionsRange 参数中允许的版本范围，并输出布尔变量。调用相同的任务来检查 umbracoApplicationUrl 参数的版本要求。有一个 MsBuild 特性应该是已知的-字符'<'和'【T4]'必须进行转义。符号“<”表示为“<”。符号“>”表示为“>”。检查版本后，我们必须使用 XmlTransform 任务修改 umbracoSettings.config，因为(惊喜！！！)用于向属性添加 URL 的 XmlPoke 任务无法插入缺少的属性。所以，添加了静态转换文件，这只是添加了一个“空”值的属性:

```
<MSBuild
Projects="$(MSBuildProjectFile)"
Properties="CustomConfigFileToTransfom=$(UmbracoSettingsConfigFullPath);CustomConfigTransformFile=$(TeamCityCiToolsPath)\CI.Builds\SharedFiles\Umbraco\umbraco.umbracosettings.umbracoApplicationUrl.config.transform"
Targets="TransformCustomConfigFile"
Condition="$(__SetUmbracoApplicationUrl)" />
<Target Name="TransformCustomConfigFile" Condition="Exists('$(CustomConfigFileToTransfom)') And Exists('$(CustomConfigTransformFile)')">
<RandomNameTask>
<Output TaskParameter="RandomName" PropertyName="RandomName" />
</RandomNameTask>
<!-- Somebody have to ensure, that he have original file copied - but this shall not be done here, IMHO -->
<!-- let us do a transformation -->
<MakeDir Directories="$(IntermediateOutputPath)$(RandomName)\"/>
<TransformXml
Source="$(CustomConfigFileToTransfom)"
Destination="$(IntermediateOutputPath)$(RandomName)\transformed.$(RandomName).config"
Transform="$(CustomConfigTransformFile)"
StackTrace="True" />
<!-- now, result of transformation shall be copied to original place -->
<Copy
SourceFiles="$(IntermediateOutputPath)$(RandomName)\transformed.$(RandomName).config"
DestinationFiles="$(CustomConfigFileToTransfom)"
OverwriteReadOnlyFiles="True" />
<!-- And intermediate results shall be deleted -->
<Delete Files="$(IntermediateOutputPath)$(RandomName)\transformed.$(RandomName).config" />
</Target> 
```

CustomTransformFile 目标:

```
 <!--
        Next target can be used to transform any input xml file by using transform file.
        You can invoke this target by following:
        <MSBuild
        Projects="$(MSBuildProjectFile)"
        Properties="CustomConfigFileToTransfom=$(TeamCityCiToolsPath)\CI.Builds\SharedFiles\Umbraco\umbraco.web.config;CustomConfigTransformFile=$(TeamCityCiToolsPath)\CI.Builds\SharedFiles\Umbraco\umbraco.web.config.transform"
        Targets="TransformCustomConfigFile" />

        Idea is that you a passing source config file and transform for it here
    -->
  <Target Name="TransformCustomConfigFile" Condition="Exists('$(CustomConfigFileToTransfom)') And Exists('$(CustomConfigTransformFile)')">
    <Message Text="************************************************************************" />
    <Message Text="TransformCustomConfigFile is runing :)" Importance="high" />
    <Message Text="Transform file is $(CustomConfigTransformFile)" Importance="high" />
    <Message Text="Transformimg following file - $(CustomConfigFileToTransfom)" Importance="high" />
    <Message Text="************************************************************************" />
    <RandomNameTask>
      <Output TaskParameter="RandomName" PropertyName="RandomName" />
    </RandomNameTask>
    <!-- Somebody have to ensure, that he have original file copied - but this shall not be done here, IMHO -->
    <!-- let us do a transformation -->
    <MakeDir Directories="$(IntermediateOutputPath)$(RandomName)\"/>
    <PropertyGroup>
      <__fileToTransform>$(IntermediateOutputPath)$(RandomName)\source.$(RandomName).config</__fileToTransform>
      <__transformedFile>$(IntermediateOutputPath)$(RandomName)\transformed.$(RandomName).config</__transformedFile>
    </PropertyGroup>
    <!-- Copy original file for transformation to avoid locking issues -->
    <Copy
            SourceFiles="$(CustomConfigFileToTransfom)"
            DestinationFiles="$(__fileToTransform)"
            OverwriteReadOnlyFiles="True" />
    <TransformXml
            Source="$(__fileToTransform)"
            Destination="$(__transformedFile)"
            Transform="$(CustomConfigTransformFile)"
            StackTrace="True" />
    <!-- now, result of transformation shall be copied to original place -->
    <Delete Files="$(CustomConfigFileToTransfom)" ContinueOnError="True" />
    <Copy
            SourceFiles="$(__transformedFile)"
            DestinationFiles="$(CustomConfigFileToTransfom)"
            OverwriteReadOnlyFiles="True" />
    <!-- And intermediate results shall be deleted -->
    <Delete Files="$(__transformedFile)" ContinueOnError="True" />
    <Delete Files="$(__fileToTransform)" ContinueOnError="True" />

    <Message Text="Config after TransformCustomConfigFile" />
    <MSBuild Projects="$(MSBuildProjectFile)" Properties="_FileToRead=$(CustomConfigFileToTransfom)" Targets="_ListFileContent" ContinueOnError="True" />
    <Message Text="End of config after TransformCustomConfigFile" />
  </Target> 
```

最后一项任务是使用 system . website . hostname Teamcity 变量和 web.config 中的 umbracoPath 键的值，在附加 MsBuild 任务的帮助下构建访问 Umbraco 的 URL。我不得不构建这样一个麻烦的解决方案，因为属性值看起来有所不同:

*   baseUrl 不应包含模式，但应包含端口(例如，它应类似于 mysite.com:80/Umbraco)
*   umbracoApplicationUrl 应该只是完全限定的域名(http://mysite.com/umbraco)——()

这是通过使用两个内置的 MsBuild XmlPoke 任务来完成的:

```
<XmlPoke Namespaces="&lt;Namespace Prefix='msb' Uri='http://schemas.microsoft.com/developer/msbuild/2003'/&gt;"
XmlInputPath="$(UmbracoSettingsConfigFullPath)"
Query="//settings/scheduledTasks/@baseUrl"
Value="$(BaseUrl)"
Condition="$(__SetScheduledTasksBaseUrl)" />
<XmlPoke Namespaces="&lt;Namespace Prefix='msb' Uri='http://schemas.microsoft.com/developer/msbuild/2003'/&gt;"
XmlInputPath="$(UmbracoSettingsConfigFullPath)"
Query="//settings/web.routing/@umbracoApplicationUrl"
Value="$(UmbracoApplicationUrl)"
Condition="$(__SetUmbracoApplicationUrl)" /> 
```
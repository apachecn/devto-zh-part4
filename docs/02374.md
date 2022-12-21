# Gatsby 与 WordPress -缓存下载的媒体图像以减少构建时间

> 原文：<https://dev.to/robmarshall/gatsby-with-wordpress-caching-downloaded-media-images-to-reduce-build-time-1d2m>

我一直在和 WordPress 一起工作，作为后端 CMS(众多微服务之一)和 Gatsby 来开发这个网站。为了连接两者，我使用了一个名为 [WP GraphQL](https://github.com/wp-graphql/wp-graphql) 的 WordPress 插件和一个名为 [Gatsby Source GraphQL](https://www.gatsbyjs.org/packages/gatsby-source-graphql/) 的 Gatsby 插件。除了能够使用任何图像之外，一切都工作得非常出色！

经过大量搜索，我找到了来自 NeverNull 的 Henrik Wirth 的一篇文章，解释了如何使用 Gatsby 提供的 createResolvers 函数。他的文章详细介绍了将 WordPress 媒体库中的所有媒体项目放入 Gatsby 的过程。完美，现在他们可以在整个构建中使用。不幸的是，这个解决方案不缓存图像，这意味着所有的东西都要重新下载。给 Gatsby 主机和 WordPress 主机都带来了压力——增加了构建时间。

我的解决方案建立在 Henriks 的基础上，增加了一点必要的缓存。

在盖茨比节点文件中:

```
exports.createResolvers = ({
  actions,
  cache,
  createNodeId,
  createResolvers,
  getNode,
  store,
  reporter
}) => {
  const { createNode, touchNode } = actions;

  // Add all media libary images so they can be queried by
  // childImageSharp
  createResolvers({
    WPGraphQL_MediaItem: {
      imageFile: {
        type: `File`,
        async resolve(source, args, context, info) {
          if (source.sourceUrl) {
            let fileNodeID;
            let fileNode;
            let sourceModified;

            // Set the file cacheID, get it (if it has already been set)
            const mediaDataCacheKey = `wordpress-media-${source.mediaItemId}`;
            const cacheMediaData = await cache.get(mediaDataCacheKey);

            if (source.modified) {
              sourceModified = source.modified;
            }

            // If we have cached media data and it wasn't modified, reuse
            // previously created file node to not try to redownload
            if (cacheMediaData && sourceModified === cacheMediaData.modified) {
              fileNode = getNode(cacheMediaData.fileNodeID);

              // check if node still exists in cache
              // it could be removed if image was made private
              if (fileNode) {
                fileNodeID = cacheMediaData.fileNodeID;
                // https://www.gatsbyjs.org/docs/node-creation/#freshstale-nodes
                touchNode({
                  nodeId: fileNodeID
                });
              }
            }

            // If we don't have cached data, download the file
            if (!fileNodeID) {
              try {
                // Get the filenode
                fileNode = await createRemoteFileNode({
                  url: source.sourceUrl,
                  store,
                  cache,
                  createNode,
                  createNodeId,
                  reporter
                });

                if (fileNode) {
                  fileNodeID = fileNode.id;

                  await cache.set(mediaDataCacheKey, {
                    fileNodeID,
                    modified: sourceModified
                  });
                }
              } catch (e) {
                // Ignore
                console.log(e);
                return null;
              }
            }

            if (fileNode) {
              return fileNode;
            }
          }
          return null;
        }
      }
    }
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这通过以下方式实现:

1.  查找 WP GraphQL 媒体项节点–WP graph QL _ Media Item–并遍历此内的所有 imageFile 节点。
2.  检查以确保图像有一个源 URL。
3.  基于图像创建缓存 ID，并检查 ID 是否已经存在。如果有，将检查该映像是否较新。
4.  如果存在一个不是最新的图像，只需快速刷新节点(这样它就不会被删除)。
5.  如果图像不存在，获取图像并创建一个新的缓存 ID。
6.  返回预先存在的或新图像的节点

使用这个解析器时需要做一些事情。当在 GraphQL 查询中查询图像时，需要包括以下选项:

*   sourceUrl
*   mediaItemId
*   修改

这意味着获取 posts 特色图片的查询应该写成这样:

```
query GET_POSTS {
  posts {
    edges {
      node {
    featuredImage {
          sourceUrl
          mediaItemId
          modified
          imageFile {
            childImageSharp {
              fluid(maxWidth: 650) {
                base64
                aspectRatio
                src
                srcSet
                sizes
              }
            }
          }
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你遇到和我一样的问题，希望这能帮助你！这个过程可以改变，以工作与作者的头像，以及节省宝贵的带宽位。
# Java GUIs 中的浏览器集成

> 原文：<https://dev.to/angelesbroullon/browser-integration-in-java-guis-2nnm>

# 简介

几年前，我不得不面对的一个挑战是将 Java 桌面应用程序从 32 位架构迁移到 64 位架构，这可能像使用正确的 JDK、切换到正确的外部库版本和删除废弃的代码一样简单，但实际上却有一个额外的问题。该应用程序在一个面板上嵌入了一个 32 位 PDF 阅读器，这个限制已经停止了一段时间的迁移。

几年前，我不得不面对的一个挑战是将 Java 桌面应用程序从 32 位架构迁移到 64 位架构，这可能像使用正确的 JDK、切换到正确的外部库版本和删除废弃的代码一样简单，但实际上却有一个额外的问题。该应用程序在一个面板上嵌入了一个 32 位 PDF 阅读器，这个限制已经停止了一段时间的迁移。

深入到代码本身，它使用的是火狐引擎组件之一 XULRunner，所以它实际做的是“使用 32 位嵌入式火狐浏览器”。它还使用 Adobe Acrobat 生成的交互式表单，这实际上需要 Adobe Acrobat Reader 本身正确呈现，这使得 Apache PDFBox 无法完成这项任务。

当我研究运行嵌入式浏览器的原理时，我试图在我的笔记本电脑上发生的事情:当我在网上冲浪并在我的 Firefox 32 位浏览器上打开 PDF 文件时，我没有使用一些浏览器代码:我在浏览器的 Adobe Acrobat Reader X 插件中委托了这些代码。那么，为什么我们不把这个想法更进一步，通过集成使用同一个插件呢？Adobe 本身提供了这个系统，所以不会有不兼容的问题，我只需要通过操作系统配置来设置它，以获得一个变通办法，并获得我们当前运行程序的机器架构的正确版本。

让我们来看看 DJNativeSwing，这是一个位于 SWT 的库(标准小部件工具包)，它允许我们在代码中嵌入自己的浏览器。强烈推荐 SWT 库，因为它的可移植性，因为它访问本机操作系统图形用户界面，这正是我们对此类问题的要求。它也是 Java Swing 的下一个包装级别，实际上更轻更快，我以前在处理 Macromedia Flash 插件集成时已经使用它来避免问题。

作为如何配置的示例，我们将遵循两个基本步骤:

*   在 Java swing 组件上设置浏览器选项卡。
*   能够在该浏览器选项卡上打开 PDF。

* * *

# 流程

## 1。以前的设置:所需的 Maven 依赖项

让我们从基础开始:首先，我们将获得 maven 依赖项。

最初写这篇文章时，❕This 是稳定版本。

```
<dependecy>
  <group>chrriis.dj.nativeswing</group>
  <artifactId>DJNativeSwing</artifactId>
  <version>1.0.2</version>
</dependecy> 
```

## 2。构建基本的 Swing 界面

在那之后，我们将定义基本的窗口框架，带有一个选择文件的按钮(通过 FileChooser)和一个显示结果的面板。

❕Setting 将所有文本字符串作为常量，这样更容易发现它们，以便在需要时替换它们。这不是真正必要的，但是这样做可以提高可重用性。

```
import java.awt.BorderLayout;
import java.awt.Component;
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
import java.io.IOException;
import java.util.Map;

import javax.swing.BoxLayout;
import javax.swing.JButton;
import javax.swing.JFileChooser;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTextPane;
import javax.swing.SwingUtilities;
import javax.swing.filechooser.FileNameExtensionFilter;

import nativeSwing.BrowserPanel;
import pdfHandler.PdfReader;
import xmlHandler.XMLHandler;
import chrriis.common.UIUtils;
import chrriis.dj.nativeswing.swtimpl.NativeInterface;

public class DemoPDFRenderLauncher {

 private static final String TITLE = "PDF Renderer demo";
 private static final String NO_OUTPUT_MESSAGE = "No output available";
 private static final String NO_DATA_MESSAGE = "There is no data available from form";
 private static final int LENGTH = 800;
 private static final int WIDTH = 600;
 private static final String FILTER_FILES "PDF files";
 private static final String FILE_EXTENSION "pdf";

 /**
  * The main app window
  */
 private JFrame window;
 /**
  * The path of the file we will open
  */
 private String path;
 /**
  * Button for open file function
  */
 private JButton buttonOpen;
 /**
  * A browser panel
  */
 private BrowserPanel browserPanel;

/**
  * Constructor method, creates the GUI
  */
 public Launcher() {
  window = new JFrame(TITLE);
  window.getContentPane().setLayout(new BorderLayout());
  window.setSize(LENGTH, WIDTH);
  window.add(createButtonsPanel(), BorderLayout.NORTH);
  window.add(createContentPanel(), BorderLayout.CENTER);
  window.setVisible(true);
  window.addWindowListener(new WindowAdapter() {
   public void windowClosing(WindowEvent e) {
    NativeInterface.close();
    System.exit(0);
   }
  });
 }

/**
  * Creates a button panel with the action button: open a file 
  * @return the buttons panel
  */
 private Component createButtonsPanel() {
  JPanel panel = new JPanel();
  panel.setLayout(new FlowLayout());
  buttonOpen = new JButton("Open file");
  buttonOpen.addActionListener(new ButtonOpenController());
  panel.add(buttonOpen);
  return panel;
 }

/**
  * Creates a panel to render the content
  * 
  * @return the buttons panel
  */
 private Component createContentPanel() {
  JPanel panel = new JPanel();
  panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS));
  JScrollPane scrollPaneText = new JScrollPane(textPanel);
  panel.add(scrollPaneText);
  //here we will insert the DJNativeSwing panle
  browserPanel = new BrowserPanel();
  JScrollPane scrollPaneBrowser = new JScrollPane(browserPanel);
  panel.add(scrollPaneBrowser);
  return panel;
 }

/**
  * Load button controller, which launches the FileChooser.
  */
 private class ButtonOpenController implements ActionListener {
  @Override
  public void actionPerformed(ActionEvent arg0) {
   launchOpenSelectFile();
  }
 }

/**
  * Launches the FileChooser window, and invokes the pdf opener window.
  */
 private void launchOpenSelectFile() {
  JFileChooser fileChooser = new JFileChooser();
  fileChooser.setAcceptAllFileFilterUsed(false);
  FileNameExtensionFilter filter = new FileNameExtensionFilter(
    FILTER_FILES, FILE_EXTENSION);
  fileChooser.addChoosableFileFilter(filter);
  if (fileChooser.showOpenDialog(window) == JFileChooser.APPROVE_OPTION) {
   path = fileChooser.getSelectedFile().getAbsolutePath();
   browserPanel.navigate(path);
  }
 }

@SuppressWarnings("unused")
 public static void main(String[] args) {
  UIUtils.setPreferredLookAndFeel();
  NativeInterface.open();
  SwingUtilities.invokeLater(new Runnable() {
   public void run() {
    Launcher demo = new Launcher();
   }
  });
 }
} 
```

## 3。嵌入 web 浏览器

接下来让我们看看浏览器标签:如果我们只是想创建一个浏览器面板，那么写下面几行就很简单:

```
import java.awt.BorderLayout;

import javax.swing.BorderFactory;
import javax.swing.JPanel;

import chrriis.dj.nativeswing.swtimpl.components.JWebBrowser;

public class BasicBrowserPanel extends JPanel {

 private static final String TITLE = "";

/**
  * The browser will be handled in this specific component
  */
 private JWebBrowser webBrowser;

/**
  * Constructor
  */
 public BrowserPanel() {
  super(new BorderLayout());
  JPanel webBrowserPanel = new JPanel(new BorderLayout());
  webBrowserPanel.setBorder(BorderFactory.createTitledBorder(TITLE));
  webBrowser = new JWebBrowser();
  webBrowser.setBarsVisible(false);
  webBrowser.setStatusBarVisible(false);
  webBrowserPanel.add(webBrowser, BorderLayout.CENTER);
  add(webBrowserPanel, BorderLayout.CENTER);
 }

/**
  * Initializes the browser and sets a value in the URL storage
  * @param path the URL value o file path to open
  */
 public void navigate(String path) {
  webBrowser.setVisible(true);
  webBrowser.navigate(path);
 }

 /**
  * Makes the browser retrieve and render the content from the path previously stored
  */
 public String getAddress(){
  return webBrowser.getHTMLContent();
 }

 /**
  * Hides the browser controls (forward, back, home buttons...)
  */
 public void hideContent() {
  webBrowser.setVisible(false);
 }
} 
```

通过这种方式，我们得到了一个全功能的 web 浏览器，非常类似于 Eclipse IDE 中的浏览器，但是对于我们在这里尝试做的事情来说，有太多不必要的功能。因为我们只是通过在 Adobe 上委托来完成渲染过程，所以我们可以从整个定制浏览系统中删除 GUI 额外的元素，留下裸露的面板。

```
import java.awt.BorderLayout;
import java.awt.Component;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;

import javax.swing.JFrame;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.SwingUtilities;

import chrriis.dj.nativeswing.swtimpl.NativeInterface;
import chrriis.dj.nativeswing.swtimpl.components.JWebBrowser;

/**
 * Allows to launch a JFrame containing an embedded browser.
 */
public class BrowserFrameLauncher {

/**
  * Renders the file content on a browser via DJ Native Swing
  * 
  * @param path
  *            the file url if we pass as a parameter any webpage URL,
  *             the system would try to render it
  * @return a JPanel with a native browser, to render the file content
  */
 private Component createBrowserPanel(String path) {
  JWebBrowser.useXULRunnerRuntime();
  JPanel fileBrowserPanel = new JPanel(new BorderLayout());
  final JWebBrowser fileBrowser = new JWebBrowser();
  fileBrowser.setBarsVisible(false);
  fileBrowser.setStatusBarVisible(false);
  fileBrowser.navigate(path);
  fileBrowserPanel.add(fileBrowser, BorderLayout.CENTER);
  return fileBrowserPanel;
 }

} 
```

### 4。设置最后一块拼图:获取 PDF 本身

最后，让我们做最后的润色来打开一个 PDF。我们实际上正在做的是将 PDF 文件路径放入浏览器，所以最终我们在老朋友 XULRunner 上有了一个新的层，但这为我们提供了一种通过“正确的架构版本”SWT 库集成插件的方法。因此，作为结论，我们能够连接到“正确的架构版本”插件，修复我们的渲染问题，并使我们一劳永逸地独立于 32 位平台。

```
// excerpt from BrowserFrameLauncherPDF.java

private static final String CLOSING_MESSAGE = "Do you really want to close the file?";
 private static final String RENDERED_TITLE = "PDF Renderer demo - Embed Browser";

/**
  * Opens a file and shows it content in a JFrame.
  * 
  * @param path
  *            the url of the file to open in a JFrame
  */
 public static void openPDF(final String path) {
  NativeInterface.open();
  SwingUtilities.invokeLater(new Runnable() {
   public void run() {
    final JFrame frame = new JFrame(RENDERED_TITLE);
    frame.setLocation(0, 0);
    //we may set up a default size for this test
    //frame.setSize(800, 600);
    frame.setVisible(true);
    frame.add(createBrowserPanel(path));

    //a window listener would allow us to control the closing actions
    frame.addWindowListener(new WindowAdapter() {
     public void windowClosing(WindowEvent e) {
      int i = JOptionPane.showConfirmDialog(frame,CLOSING_MESSAGE);
      if (i == 0) {
       NativeInterface.close();
      }
     }
    });
   }
  });
} 
```

❗️请注意 NativeInterface.open()行，以确保正确引导组件，并线程化该组件，以避免其他进程干扰渲染。
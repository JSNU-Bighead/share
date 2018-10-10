# 关于学习electron的理解
## 单页面应用 + electron
其实与electron无关，vue使用hash路由来管理单页面，本地也可以直接打开index.html。就可以将index.html作为新开窗口的loadFile。在这儿里有个electron-vue的脚手架，在vue-cli集成了electron，开发过程热加载，并集成了打包命令可以直接打包成app。
## electron-packager 和 electron-builder
这两个都是用来打包成app的，打包工具，前者打包成绿色免安装版，后者打包成安装文件。

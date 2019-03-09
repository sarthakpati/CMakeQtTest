# Minimal CMake Template for Qt 5 Projects

[As I've briefly introduced](https://www.euler.kr/trl/2014/08/19/minimal-cmake-template/), this is a minimal, but complete and production-ready CMake project template for Qt 5 projects. This may be a starting point for your new application. If that is not intent to be a cross-platform project, you can clean up that stuffs from the template.

If you want to deploy your application on macOS, I would recommend you read below document.

[Qt for macOS - Deployment](https://doc.qt.io/qt-5/macos-deployment.html)

## Features
  * Handling all possible Qt stuffs (UI, resources, ~~translations~~)
  * macOS bundle
  * Windows executable icon

## References
  * CMake manual: https://qt-project.org/doc/qt-5.0/qtdoc/cmake-manual.html
  * Using CMake with Qt 5: https://www.kdab.com/using-cmake-with-qt-5/
  * https://stackoverflow.com/questions/51556088/cmake-copy-dependencies-to-executable-output-path/51569345#51569345
  * https://riptutorial.com/qt/example/24344/deploying-on-mac

# 项目 C++代码规范

C++ 项目规范有如下几个可以参考的:

1. [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)
2. [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)

实践中, 代码规范的内容往往比较多, 难以完全掌握, 所以我们在项目中会使用代码检查工具来规范代码风格, 并且借助最佳实践来提高代码质量.

本项目使用自动化的代码检查工具来规范代码风格, 在提交代码之前请确保新代码通过了代码检查.

1. clang-format

   - 使用 clang-format 来格式化代码.
   - 配置文件在项目根目录下的 `.clang-format` 文件.

2. clang-tidy

   - 使用 clang-tidy 来执行静态检查, 工具会检查代码风格, 对于常见的代码缺陷进行提示.
   - 请参考项目根目录下的 `.clang-tidy` 文件.

3. sanitizers

   - 使用 sanitizers 来检查内存泄漏, 内存越界等问题. 这个工具是运行时检查, 发现错误会终止程序并打印错误信息.
   - 使用 Debug 模式编译代码, 并指定 sanitizers 来检查内存泄漏.

# watchermen 构建

目前 watchermen 支持两种构建方式：

- 使用 vcpkg 构建
- 使用传统的方式构建

## 使用 vcpkg 构建

### 使用`vcpkg`来管理第三方依赖

#### 安装 vcpkg, 这个工具可被多个项目共享

```bash
git clone https://github.com/microsoft/vcpkg.git
export VCPKG_ROOT=$PWD/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
```

#### 安装依赖

```bash
cd watchermen
$VCPKG_ROOT/vcpkg install
```

#### 使用 vcpkg 工具链(推荐方式)

- 可以使用简单的方式, 通过`CMakePresets.json`

  ```bash
  cmake --preset default
  ```

- 或者通过设置变量`CMAKE_TOOLCHAIN_FILE`来使用 vcpkg 的工具链

  ```bash
  cd watchermen
  cmake -S . -B /tmp/build-with-vcpkg -DCMAKE_TOOLCHAIN_FILE=$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake
  cmake --build /tmp/build-with-vcpkg
  ```

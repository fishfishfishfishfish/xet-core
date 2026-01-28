现在我需要总结从CI工作流中获取的信息，为用户提供详细的Rust工具链安装和构建xet-core的步骤。根据GitHub Actions CI工作流，我为您整理了安装Rust工具链和构建xet-core的详细步骤：

1. 安装Rust工具链
安装![rustup](https://rust-lang.org/zh-CN/tools/install/)
首先需要安装rustup，它是Rust的版本管理工具：
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
安装完成后，按照提示将rustup添加到PATH环境变量中。

安装特定版本的Rust工具链
根据CI工作流，xet-core主要使用Rust 1.89.0：


```bash
rustup install 1.89.0
rustup default 1.89.0
```
安装必要的组件：


```bash
rustup component add clippy
```
2. 克隆仓库
```bash
git clone https://github.com/xetdata/xet-core.git
cd xet-core
```

3. 安装依赖
安装Git LFS
在Linux上：
```bash
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
sudo apt-get install git-lfs
git lfs install
```
在macOS上：
```bash
brew install git-lfs
git lfs install
```
4. 构建和测试
运行代码检查
```bash
cargo clippy -r --verbose -- -D warnings
```
构建和测试
```bash
cargo test --verbose --no-fail-fast --features "strict git-xet-for-integration-test"
```
5. WASM构建（可选）
如果需要构建WASM版本，还需要：
```bash
# 安装nightly工具链
rustup install nightly
rustup component add rust-src --toolchain nightly
rustup target add wasm32-unknown-unknown --toolchain nightly

# 安装wasm-bindgen-cli和wasm-pack
cargo install --version 0.2.100 wasm-bindgen-cli
cargo install --version 0.13.1 wasm-pack

# 构建WASM
echo "Building hf_xet_thin_wasm..."
cd hf_xet_thin_wasm && ./build_wasm.sh && cd ..

echo "Building hf_xet_wasm..."
cd hf_xet_wasm && ./build_wasm.sh && cd ..
```

6. 代码格式化（可选）
```bash
# 安装nightly工具链和rustfmt组件
rustup install nightly
rustup component add rustfmt --toolchain nightly

# 检查代码格式
cargo +nightly fmt --manifest-path ./Cargo.toml --all -- --check
```
构建配置说明
- 主要构建使用Rust 1.89.0版本
- 测试时启用了strict和git-xet-for-integration-test特性
- WASM构建需要nightly工具链和额外的依赖
- 这些步骤与CI工作流中使用的步骤保持一致，确保您能够成功构建和测试xet-core项目。



# 运行本地服务器测试
```bash
RUST_BACKTRACE=full RUST_LOG=info HF_XET_LOG_FILE=/tmp/xet.log cargo test -p cas_client simulation::local_server::server::tests::test_local_server -- --nocapture
```
> 如果您同时向 Cargo 和二进制文件传递参数，-- 之后的参数将传递给二进制文件，-- 之前的参数则传递给 Cargo。


```bash
cargo test -p cas_client simulation -- --nocapture
```

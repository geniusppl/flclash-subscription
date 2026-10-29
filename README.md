# FlClash 自动更新订阅模板

此模板会定期从公开节点列表抓取原始订阅，用 `subconverter-ng` 转换成 Mihomo/Clash Meta YAML，并将结果提交为本仓库的 `flclash.yaml`。FlClash 之后订阅这个固定 Raw 地址并刷新即可获取新版本。

## 部署

1. 在 GitHub 新建一个 **Public** 仓库，例如 `flclash-subscription`。公开仓库是为了让 FlClash 无需登录即可读取 `flclash.yaml`。公开文件中会包含上游公开节点配置。
2. 将本模板中的 `README.md`、`.github/workflows/build-subscription.yml` 上传到仓库，默认分支使用 `main`。
3. 打开仓库的 **Actions** 标签页，若首次使用需点击启用工作流。选择 **Build FlClash subscription**，点击 **Run workflow** 执行首次生成。
4. 等首次运行成功后，仓库根目录应出现 `flclash.yaml`。固定订阅地址为：

   ```text
   https://raw.githubusercontent.com/你的GitHub用户名/flclash-subscription/main/flclash.yaml
   ```

5. 在 FlClash 中添加配置/订阅，粘贴上面的 Raw 地址。也可以用官方导入链接格式：

   ```text
   clash://install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2F你的GitHub用户名%2Fflclash-subscription%2Fmain%2Fflclash.yaml
   ```

   把 `你的GitHub用户名` 替换成实际用户名。若 `clash://` 链接未被系统唤起，直接在 FlClash 内添加订阅 URL。

工作流计划每 15 分钟尝试更新一次。GitHub 定时任务可能延迟；FlClash 也需要按客户端设置刷新订阅。工作流执行时会在 GitHub Actions 临时运行器上启动转换器容器，不需要自己维护服务器。

## 更换上游列表

默认合并 `Hidashimora/free-vpn-anti-rkn` 中被上游页面推荐的 1、6、22–34 号短列表。上游配置可能过期或无法从你的网络连接；列表合并只能增加来源覆盖，不能保证节点在线。需要调整时，编辑 `.github/workflows/build-subscription.yml` 的 `SOURCE_URL` 列表。

## 注意

- 这只是格式转换与自动发布，不会审计节点、保证在线或提高节点安全性。
- 节点配置来自第三方公开源。节点运营者可能观察经其转发的连接信息；不要把未知节点当作可信 VPN 服务。
- 输出订阅和节点配置会公开在你的 GitHub 仓库中。不要将个人订阅令牌、密码或任何私有配置放入此公开项目。
- 项目用到 GitHub Actions 和 `ghcr.io/jungley8/subconverter-ng:latest`。容器由 GitHub Actions 临时运行，不会把输出发布到转换器网站；不过工作流依赖该上游开源转换器镜像。

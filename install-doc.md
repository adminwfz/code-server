1. 安装过程：
   • 已下载 `code-server_4.99.3_amd64.deb` 到缓存目录 `~/.cache/code-server/`

   • 已通过 `dpkg` 安装 `.deb` 包

   • 安装成功，但未自动启动服务


2. 验证安装：
   ```bash
   code-server --version  # 检查版本
   systemctl status code-server@$USER  # 查看服务状态（如果已启用）
   ```

---

**后续操作建议**
1. **启动 code-server**
• 手动启动（临时运行）：

  ```bash
  code-server
  ```
  默认访问地址：`http://localhost:8080`（需替换 `localhost` 为服务器IP）

• 后台服务启动（推荐）：

  ```bash
  sudo systemctl enable --now code-server@$USER
  ```
  • 启用开机自启：`enable`

  • 立即启动服务：`--now`

  • 访问地址：`http://<服务器IP>:8080`


2. **防火墙配置**
如果无法访问，需开放端口（默认 `8080`）：
```bash
sudo ufw allow 8080/tcp
sudo ufw reload
```

3. **修改默认端口或密码**
• 修改配置文件（通常位于 `~/.config/code-server/config.yaml`）：

  ```yaml
  bind-addr: 0.0.0.0:8080  # 改为其他端口如 8443
  auth: password           # 启用密码验证
  password: yourpassword   # 设置密码（可选）
  ```
• 重启服务生效：

  ```bash
  sudo systemctl restart code-server@$USER
  ```

4. **HTTPS 支持（可选）**
通过反向代理（如 Nginx）配置 HTTPS：
```nginx
server {
    listen 443 ssl;
    server_name yourdomain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

**常见问题**
1. 端口冲突：
   • 如果 `8080` 被占用，修改 `bind-addr` 为其他端口（如 `8443`）。


2. 权限问题：
   • 确保用户对 `~/.config/code-server` 有读写权限。


3. 服务未启动：
   • 检查日志：

     ```bash
     journalctl -u code-server@$USER -f
     ```

---

**总结**
您已成功安装 `code-server`，下一步建议：
1. 启动服务并验证访问。
2. 根据需求配置防火墙、端口和密码。
3. 如需生产环境部署，建议通过反向代理 + HTTPS 加强安全性。

如果有其他需求（如自定义域名、文件挂载等），可以进一步说明！

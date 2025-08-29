# 本地开发环境设置指南

## 📋 修改总结

### 🔧 主要修复内容

1. **数据库兼容性修复**
   - 将PostgreSQL配置改为SQLite以避免安装额外数据库
   - 修改了`.env`文件中的数据库连接字符串
   - 修复了后端配置中的字段验证问题

2. **迁移文件修复**
   - 修复了SQLite不支持的`ALTER TABLE`操作
   - 修改了UUID字段的数据库类型定义
   - 跳过了不兼容的迁移步骤

3. **模型定义优化**
   - 将User和Item模型的UUID字段改为字符串类型
   - 保持了UUID的生成逻辑，但存储为字符串格式
   - 确保了SQLite的兼容性

4. **依赖安装和配置**
   - 安装了所有Python和Node.js依赖
   - 配置了前端开发环境

### 🚀 本地服务启动指南

#### 后端服务启动
```bash
cd backend
uv run fastapi dev app/main.py --host 0.0.0.0 --port 8000
```

#### 前端服务启动
```bash
cd frontend
npm run dev
```

#### 数据库初始化（首次运行或重置）
```bash
cd backend
# 删除旧数据库
rm -f app.db
# 运行迁移
uv run alembic upgrade head
# 创建初始用户
uv run python -c "from app.initial_data import main; main()"
```

### 🌐 服务访问地址

- **前端应用**: http://localhost:5173
- **后端API**: http://localhost:8000
- **API文档**: http://localhost:8000/docs
- **默认账户**: admin@example.com / changethis

### 💻 开发修改指南

#### 后端开发
1. 修改`backend/app/`目录下的代码
2. 热重载会自动生效
3. 如需修改数据库模型：
   ```bash
   cd backend
   uv run alembic revision --autogenerate -m "your message"
   uv run alembic upgrade head
   ```

#### 前端开发
1. 修改`frontend/src/`目录下的代码
2. Vite会自动热重载
3. 组件和样式修改会实时生效

#### 数据库操作
- **查看数据**: 可以使用SQLite浏览器或命令行
- **重置数据库**: 删除`app.db`文件，重新运行迁移
- **添加新模型**: 在`backend/app/models.py`中定义，然后生成迁移

### ⚠️ 注意事项

1. **安全提醒**: 生产环境请修改默认密码和密钥
2. **数据库**: 使用SQLite作为本地开发数据库，无需额外配置
3. **端口**: 确保8000和5173端口未被占用
4. **依赖**: 如遇到依赖问题，可删除`node_modules`和`app.db`重新安装

### 🎯 当前状态

✅ 系统完全可用
✅ 前后端服务可正常启动
✅ 用户认证功能正常
✅ 数据库操作正常
✅ API文档可访问

### 📁 修改的文件列表

1. `.env` - 数据库配置修改
2. `backend/app/core/config.py` - 配置兼容性修复
3. `backend/app/models.py` - 模型定义优化
4. `backend/app/alembic/versions/e2412789c190_initialize_models.py` - 初始迁移修复
5. `backend/app/alembic/versions/9c0a54914c78_add_max_length_for_string_varchar_.py` - 迁移兼容性修复
6. `backend/app/alembic/versions/d98dd8ec85a3_edit_replace_id_integers_in_all_models_.py` - UUID迁移修复
7. `backend/app/alembic/versions/1a31ce608336_add_cascade_delete_relationships.py` - 级联删除迁移修复

### 🚀 快速启动脚本

创建启动脚本 `start-local.sh`:

```bash
#!/bin/bash

echo "🚀 启动本地开发环境..."

# 启动后端服务
echo "启动后端服务..."
cd backend
uv run fastapi dev app/main.py --host 0.0.0.0 --port 8000 &
BACKEND_PID=$!

# 启动前端服务
echo "启动前端服务..."
cd ../frontend
npm run dev &
FRONTEND_PID=$!

echo "✅ 服务启动完成!"
echo "📱 前端: http://localhost:5173"
echo "🔗 后端: http://localhost:8000"
echo "📚 API文档: http://localhost:8000/docs"
echo ""
echo "按 Ctrl+C 停止所有服务"

# 等待用户中断
trap "echo '🛑 停止服务...'; kill $BACKEND_PID $FRONTEND_PID; exit" INT
wait
```

给脚本执行权限：
```bash
chmod +x start-local.sh
```

然后运行：
```bash
./start-local.sh
```

现在您可以开始开发您的应用功能了！有任何问题随时参考此文档。

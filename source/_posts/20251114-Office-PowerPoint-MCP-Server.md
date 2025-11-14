---
title: Office-PowerPoint-MCP-Server
date: 2025-11-14 14:49:24
tags:
  - ppt
categories: MCP
---
Office-PowerPoint-MCP-Server 是一款基于 MCP（Model Context Protocol）协议的 PowerPoint 操作服务器，内部依赖 `python-pptx` 库，能够通过标准化的工具接口完成创建、编辑、保存、填充、排版等各类演示文稿自动化操作。它支持对 `.pptx` 文件的完整 Round-trip，能够在保持原始结构与样式的基础上，灵活插入文本、图像、表格和图表等元素。

## 核心功能一览

- **完整 Round-trip**：无论是打开还是保存，都能保留原始文档的所有元素与属性。
- **新增幻灯片**：支持根据布局索引添加更多幻灯片，轻松扩展演示文稿结构。
- **占位符文本填充**：可批量或单条替换文本占位符，生成要点式、列表式幻灯片。
- **图片管理**：在幻灯片任意位置以指定大小插入图片，支持本地文件与 Base64 数据流。
- **文本框操作**：添加文本框，设置字体大小、加粗等样式属性，实现个性排版。
- **表格与图表**：支持插入表格并格式化单元格，添加柱状图、折线图、饼图等多种图表。
- **自动图形**：提供多种预定义形状（多边形、流程图节点等），助你绘制流程与架构图。
- **文档属性管理**：可读取与修改文档的标题、主题等核心属性，满足元数据需求。

## 安装与部署

### 前置条件

**Python 版本**：需 Python 3.10 及以上。

**包管理器**：系统已安装 `pip`，并能访问 PyPI 或本地私有源。

### 通过 Smithery 一键安装

```
npx -y @smithery/cli install @GongRzhe/Office-PowerPoint-MCP-Server --client claude
```

此方法适合已在使用 Smithery 平台的团队，一行命令即可完成依赖下载、环境配置与服务集成。

### 脚本式安装（推荐）

项目附带了 `setup_mcp.py` 脚本，可自动化完成全流程：

```
python setup_mcp.py
```

脚本功能包括：

1. 检测 Python 版本和 `pip` 可用性；

2. 询问安装选项（PyPI 安装或本地开发模式）；

3. 安装所需依赖；

4. 生成 MCP 协议配置文件；

5. 指导 Claude Desktop 或其他客户端完成集成。

### 手动安装步骤

对于喜欢手动掌控每一步的开发者，可按以下流程执行：

1. **克隆仓库**

```
git clone https://github.com/GongRzhe/Office-PowerPoint-MCP-Server.gitcd Office-PowerPoint-MCP-Server
```

1. **安装依赖**

```
pip install -r requirements.txt
```

1. **赋予执行权限**

```
chmod +x ppt_mcp_server.py
```

完成以上步骤后，即可启动服务器并调用各项工具。

## MCP 协议简介与配置示例

MCP（Model Context Protocol）是一种轻量级 JSON-RPC 风格协议，旨在规范化客户端与服务器之间的工具调用。配置文件示例如下。

### 本地 Python 服务配置

```
{  
    "mcpServers": {  
        "ppt": {  
            "command": "python",  
            "args": [  
                "/path/to/ppt_mcp_server.py"  
            ],  
            "env": {  
  
            }  
        }  
    }  
}
```

该配置指定使用 Python 命令启动 `ppt_mcp_server.py`，无需额外环境变量。

### UVX 无需本地安装方式

```
{  
    "mcpServers": {  
        "ppt": {  
            "command": "uvx",  
            "args": [  
                "--from",  
                "office-powerpoint-mcp-server",  
                "ppt_mcp_server"  
            ],  
            "env": {  
  
            }  
        }  
    }  
}
```

UVX 客户端会从云端拉取对应工具，无需在本地安装源码，适合免维护场景。

## 常用工具与接口说明

以下小节将按功能模块细致说明各工具及参数。

### 文档创建与打开

#### create_presentation

功能：生成一个空白演示文稿并返回 `presentation_id`。

参数：无。

返回：`{"presentation_id": "xxx"}`

#### open_presentation

功能：打开已有 `.pptx` 文件。

参数：`{"path": "/full/path/to/file.pptx"}`

返回：`{"presentation_id": "xxx"}`

#### save_presentation

功能：将内存中的演示文稿保存到指定路径。

参数：`{"presentation_id":"xxx", "path":"/dest.pptx"}`

返回：操作结果状态。

### 幻灯片操作

#### add_slide

功能：根据布局索引添加新幻灯片。

参数：

- `presentation_id`：文档 ID
- `layout_index`：布局编号（如 0 为封面、1 为标题+内容等）
- `title`（可选）：幻灯片标题

返回：新增幻灯片的 `slide_id`。

#### get_slide_info

功能：查询幻灯片详情，包括占位符、形状列表等。

参数：`{"presentation_id":"xxx"}`

返回：幻灯片元数据数组。

### 文本与占位符填充

#### populate_placeholder

功能：将指定占位符填充为文本。

参数：

- `slide_id`
- `placeholder_index`
- `text`

示例：填充文章要点列表。

#### add_bullet_points

功能：在文本框内以项目符号形式添加多行文本。

参数：

- `slide_id`
- `text_list`：字符串数组

适用场景：制作分点陈述或总结页。

#### add_textbox

功能：自定义位置与大小添加文本框。

参数：

- `left`, `top`, `width`, `height`（单位：EMU）
- `text`, `font_size`, `bold` 等样式属性。
### 图表与表格

**add_table**
#### 

功能：插入表格并返回表格对象 ID。

参数：

- `cols`, `rows`
- `left`, `top`, `width`, `height`

可配合 **format_table_cell** 对单元格进行格式化。

#### add_chart

功能：添加数据可视化图表。

参数：

- `chart_type`：如 `"bar"`, `"line"`, `"pie"`
- `data`：二维数组或指定格式
- `position`：图表所在坐标与尺寸

支持图例、数据标签、坐标轴设置等。

### 图片与形状

#### add_image / add_image_from_base64

功能：插入图片文件或 Base64 编码图像。

参数：`path` 或 `base64_data`，以及位置尺寸属性。

#### add_shape

功能：插入流程图形状、多边形等自动图形。

参数：`shape_type`, `left`, `top`, `width`, `height`。

可进一步设置颜色、线型、文本等。

### 文档属性管理

#### get_presentation_info

功能：获取标题、主题、作者等属性。

#### set_core_properties

功能：修改文档元数据，如标题（`title`）、主题（`subject`）。

参数：JSON 对象键值对。

## 实战示例详解

下面通过完整示例，演示如何在代码中调用 MCP 工具，实现从创建文档到填充内容、插入图表、导出文件的全流程。

### 新建演示文稿并添加标题页

```
# 调用 create_presentation
result = use_mcp_tool(
    server_name="ppt",
    tool_name="create_presentation",
    arguments={}
)
presentation_id = result["presentation_id"]
 
# 添加封面幻灯片（布局 0 通常为封面）
slide = use_mcp_tool(
    server_name="ppt",
    tool_name="add_slide",
    arguments={
        "presentation_id": presentation_id,
        "layout_index": 0,
        "title": "年度总结报告"
    }
)
slide_id = slide["slide_id"]
 
# 填充封面副标题
use_mcp_tool(
    server_name="ppt",
    tool_name="populate_placeholder",
    arguments={
        "slide_id": slide_id,
        "placeholder_index": 1,
        "text": "2025 年度业绩概览"
    }
)
```

### 批量填充数据与图表

```
# 添加数据概览幻灯片（布局 1）
slide_data = use_mcp_tool(
    server_name="ppt",
    tool_name="add_slide",
    arguments={
        "presentation_id": presentation_id,
        "layout_index": 1,
        "title": "销售数据一览"
    }
)
slide_id_data = slide_data["slide_id"]
 
# 在内容区添加柱状图
chart = use_mcp_tool(
    server_name="ppt",
    tool_name="add_chart",
    arguments={
        "slide_id": slide_id_data,
        "chart_type": "bar",
        "data": [
            ["月份", "销售额"],
            ["一月", 120],
            ["二月", 150],
            ["三月", 180]
        ],
        "left": 914400,   # 1 英寸
        "top": 1828800,   # 2 英寸
        "width": 6096000, # 6.7 英寸
        "height": 3429000 # 3.8 英寸
    }
)
```

### 自定义样式与高级布局

**自定义文本框**

```
use_mcp_tool(
    server_name="ppt",
    tool_name="add_textbox",
    arguments={
        "slide_id": slide_id_data,
        "left": 457200,  # 0.5 英寸
        "top": 914400,   # 1 英寸
        "width": 3048000,# 3.3 英寸
        "height": 914400,# 1 英寸
        "text": "注：以上数据为示例，仅供参考",
        "font_size": 12,
        "bold": False
    }
)
```

**流程图形状**

```
use_mcp_tool(
    server_name="ppt",
    tool_name="add_shape",
    arguments={
        "slide_id": slide_id_data,
        "shape_type": "flowChart_process",
        "left": 914400,
        "top": 4000000,
        "width": 2000000,
        "height": 800000
    }
)
```

## 进阶技巧与最佳实践

### 性能优化与批量处理

**复用文档对象**：尽量一次性读取/打开文档，批量操作后再统一保存，减少 I/O 开销。

**并行调用**：在支持异步或多进程环境中，可并行启动多个 MCP 会话，实现多文档并行生成。

**缓存配置**：将 MCP 配置文件缓存到内存，仅在变更时重读，提升启动效率。

### 脚本与 CI/CD 集成

将生成脚本纳入项目仓库，配置 CI 管道（如 GitHub Actions、GitLab CI）自动执行文档构建任务。

配合版本控制系统，对演示文稿版本进行追踪与差异分析。

使用容器化部署（Docker）封装运行环境，确保跨平台一致性。

### 跨平台部署注意事项

Windows 系统下需保证 PowerPoint 文件路径长度 ≤ 260 字符，避免路径截断错误。

Linux 或 macOS 环境下，注意 `python-pptx` 对 EMU 单位的换算与 DPI 设定。

若在无 GUI 环境运行，确认脚本无需调用 Office COM 接口，仅依赖 `python-pptx` 即可。

## 常见问题与排查指南

1. **启动报错 “ModuleNotFoundError: No module named ‘pptx’”**

原因：未执行 `pip install python-pptx`。


解决：执行 `pip install -r requirements.txt` 或单独安装依赖。


1. **插入图片后显示空白**


原因：图片路径错误或 Base64 格式不正确。

解决：检查 `add_image` 参数，确保 `path` 指向可读文件，或验证 Base64 数据完整性。

1. **表格格式不生效**

原因：调用 `format_table_cell` 前需确认表格已成功插入并获取表格对象。


解决：先使用 `add_table` 获取表格 ID，再依次调用 `format_table_cell`。

1. **图表数据未更新**

原因：图表数据需要与布局配合，某些布局会覆盖手动添加的图表。

解决：使用自定义布局或空白布局（layout_index 对应空白页）以避免覆盖。




[Python高手必备：用Office-PowerPoint-MCP-Server实现PPT自动化神操作 | 高效码农](https://www.xugj520.cn/archives/python-ppt-automation-mcp-server.html)
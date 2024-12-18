# H-RAG

## 环境准备
```shell
# 配置环境
cd /AIRvePFS/dair/wenluo/projects/GraphRAG-Ollama-UI
pip install requirements_graphrag.txt
```
```shell
# 激活环境
cd /AIRvePFS/dair/wenluo/projects/GraphRAG-Ollama-UI
conda activate graphrag
```

## 运行准备
```shell
# 1. 创建测试目录
mkdir -p ./ragtest_test2/input
```

```shell
# 2. 按章节切分名著，将测试数据存到input目录
process_document.ipynb
```

```shell
# 3. 初始化项目
python -m graphrag.index --init --root ./ragtest_test2
```

```shell
# 4. ragtest_all/settings.yaml, 其他平台模型复制对应的配置文件即可， 修改env文件，将extra_settings下配置的yaml文件对应模型所需要的环境变量填写上，包括LLM和Embedding模型
```

```shell
# 5. 开启ollama embedding服务
ollama serve
在另一cli运行
ollama run bge-m3
```

## 索引

```shell
# 1. 构建GraphRAG索引
防止中断从头开始，强烈建议加上resume参数
python -m graphrag.index --root ./ragtest_test2 --resume test
中途可能因为rate limit导致中途中断，配置resume参数之后，失败了重新跑一遍就可以，也可以调小yaml文件中的concurrent_requests值
该步骤可能碰到的错误见，常见问题汇总.md
```

```shell
# 2. 构建RAG索引
python rag_index.py --root ./ragtest_test2 --output hongloumeng2
```

```shell
# 3. 生成每个章节的概括并构建RAG索引
python rag_index_summary.py --root ./ragtest_test2 --output hongloumeng2
```

```shell
# 4. （optional）将已有知识图谱导入neo4j，并与GraphRAG索引生成的graph进行merge
已有图谱导入neo4j：neo4j_load_graphrag.ipynb
知识图谱merge：python graph_merge.py
```


## 查询
```shell
python app.py --root ragtest_test2
```

![UI Example](demo1.png)
选择需要的查询类型和输出文件夹后，在输入框里输出问题即可。如果选择智能搜索，系统会根据问题自动选择合适的搜索方式。

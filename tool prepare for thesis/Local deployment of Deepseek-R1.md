---
title: Local deployment of Deepseek-R1

---

## Reference
[Ollama 教學— — 在本機環境輕鬆執行 LLM 及其延伸應用（自訂 GGUF 模型、Open-WebUI 及 Code Copilot）](https://medium.com/@NeroHin/ollama-%E5%9C%A8%E6%9C%AC%E6%A9%9F%E7%92%B0%E5%A2%83%E8%BC%95%E9%AC%86%E5%9F%B7%E8%A1%8C-llm-%E5%8F%8A%E5%85%B6%E5%BB%B6%E4%BC%B8%E6%87%89%E7%94%A8-%E8%87%AA%E8%A8%82-taide-lx-7b-chat-%E6%A8%A1%E5%9E%8B-open-webui-%E5%8F%8A-code-copilot-ba11e4a1e627)
## Local deployment of Deepseek-R1
Environment:
- win10 
- 16G RAM
- GPU : HD Graphics 530 / 16G
---

## Install Ollama
Tips: [Ollama](https://ollama.com/download/windows) is a tool that allows users to easily deploy and run large language models on their local machines.
![image](https://hackmd.io/_uploads/HybyaSe9yx.png)

![image](https://hackmd.io/_uploads/rykbaHxqyg.png)

---
- Verify whether the installation is successful. 
- Enter ollama in the black window and the above information appears, which means it is successful.

![image](https://hackmd.io/_uploads/SJzdCSlqJe.png)
- [Model List](https://ollama.com/library?sort=popular)
```bash=
ollama run deepseek-r1:7b
```

```bash=
ollama run llama2:7b
```

![image](https://hackmd.io/_uploads/Sy4ek8xqJx.png)
![image](https://hackmd.io/_uploads/HyyF-Ugckl.png)

![image](https://hackmd.io/_uploads/rJDdN8x91x.png)

![image](https://hackmd.io/_uploads/SJMMBLecyl.png)

## Commands
- Leave
```bash=
/bye
```
![image](https://hackmd.io/_uploads/SkDEDIgqJl.png)

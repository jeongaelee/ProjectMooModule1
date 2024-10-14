## Assistants function calling (Bing Search에서 검색)

### Overview

본 노트북에서는, [Bing Search APIs](https://www.microsoft.com/bing/apis/llm) and [function calling](https://learn.microsoft.com/azure/ai-services/openai/how-to/function-calling?tabs=python)와 [function calling](https://learn.microsoft.com/azure/ai-services/openai/how-to/function-calling?tabs=python)을 이용하여 Azure OpenAI 모델을 웹 데이터로부터 그라운드 하는 방법을 보여줍니다. 이 방법은 모델이 웹에서 검색하는 최신 데이터에 접근하는 좋은 방안입니다.

이 샘플은 Function Calling 기능과 서치 기반의 그라운딩을 배우려고 하는 개발자들과 데이터 과학자들에게 유용할 것 입니다.

### 학습 목표

이 실습의 주요 목적은 향상된 기능을 위해 LLM에서 Function Calling을 어떻게 사용하는지 보여주는 것 입니다.

이 실습에서 사용된 서비스:

- Azure OpenAI Service - you can apply for access [here](https://go.microsoft.com/fwlink/?linkid=2222006)
- Bing Search - [Bing Search resource](https://learn.microsoft.com/en-us/bing/search-apis/bing-web-search/create-bing-search-service-resource) 

### 프로그래밍 언어

- Python

### 소요시간: 10분
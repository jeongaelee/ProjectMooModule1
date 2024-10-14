# Azure OpenAI Assistants

Azure OpenAI Assistants는 Code Interpreter, Function Calling등의 도구를 통해 사용자 맞춤형 AI 어시스턴트를 생성할 수 있게 해줍니다.

## 사전 요구 사항
* [Visual Studio Code 설치] (https://code.visualstudio.com/)

## 1. Azure OpenAI Studio의 Assistant Playgound에서 Code interpreter 사용해보기

### Azure 리소스 생성
1. [Azure Portal](https://portal.azure.com/)으로 접속하여 새 리소스 만들기를 합니다.

    <img src="images/step01.png" width="300"/>

2. "bing search v7"을 검색하고 생성합니다.

    <img src="images/step02.png" width="500"/>

3. Bing Search 리소스를 생성합니다. 아래와 같이 리소스 그룹을 새로 만들고, 지역은 "West US"를 선택하고, 가격 티어는 F1을 선택한 후 "Review + create"를 누릅니다.

    <img src="images/step03.png" width="700"/>

4. 아래와 같이 "azure openai"로 검색하여 Azure OpenAI 리소스를 생성합니다.

    <img src="images/step04.png" width="300"/>

5. 3번에서 생성한 리소스 그룹을 선택하고, 지역은 "West US"를 선택합니다. 가격 티어는 "Standard S0"을 선택한 후 "Next"를 누룹니다. Network 및 나머지 과정은 디폴트 값으로 선택 후 "Review + create"를 누릅니다.

    <img src="images/step05.png" width="700"/>

6. 리소스 배포가 완료되면 "Go to resource"를 클릭하여 Azure OpenAI로 이동합니다.

    <img src="images/step06.png" width="500"/>

### Azure OpenAI Studio에서 Model Deployment, Assistant 생성 및 사용
7. Azure OpenAI 리소스에서 "Go to Azure OpenAI Studio"를 클릭하여 Azure OpenAI Studio로 이동합니다.

    <img src="images/step07.png" width="600"/>

8. Azure OpenAI Studio에서 Assistants Playground를 선택합니다.

    <img src="images/step08.png" width="700"/>

9. Assistants Playground에서 Assistant를 생성하기 전에, 먼저 모델 배포 (Deployment)를 해야합니다. "+ Create new deployment"에서 "From base models"를 선택한 후 모델 배포를 시작합니다.

    <img src="images/step09.png" width="500"/>

10. "gpt-4o" 모델을 선택합니다. gpt-4등 다른 모델을 선택하여도 되지만, 지역 및 배포타입에 따라서 선택이 되지 않을 수도 있습니다. 지역별 모델의 제공 여부는 [Azure OpenAI Service models] (https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models?tabs=python-secure)를 참고하시기 바랍니다.

    <img src="images/step10.png" width="600"/>

11. Deployment namd, model version, Deployment type을 선택합니다. Deployment type은 Global Standard와 Standard 중 선택하세요. 다음 단계에서 수학 계산을 위한 Assistant를 생성할 예정이므로, Tokens per Minute Rate Limit을 올려줍니다. 모든 항목을 입력/선택 한 후에 "Deploy" 버튼을 누릅니다.

    <img src="images/step11.png" width="600"/>

12. "+ New assistant"를 클릭하여 새로운 Assistant를 셋업합니다. 위에서 Deploy한 모델을 선택하고, Assistant 이름을 넣어줍니다. 수학 문제를 푸는 도움을 주는 Assistant의 예시를 테스트 할 예정이므로, "Math Assistant"라고 넣어줍니다. Instruction에는 "You are an AI assistant that can write to help answer math questions."를 적어줍니다.

    <img src="images/step12.png" width="500"/>

13. Tools 메뉴에서 "Code interpreter"를 켭니다. 오른쪽 하단의 프롬프트 입력창에 "I need to solve the equation '3x + 11 = 14'. Can you help me?"를 입력합니다.

    <img src="images/step13.png" width="700"/>

14. 답변이 정상적으로 오는 것을 확인합니다. Assistant를 호출하여 응답을 준 것인지는 확인하기 위하여, "Logs"를 클릭하여 로그에서 "/openai/assistants"를 호출 한 것을 확인합니다.

    <img src="images/step14.png" width="700"/>

## 2. Python 코드로 Assistants 구현 해 보기

### Assistants Function Calling (Bing Search에서 검색) 구현 해 보기 (예상 소요시간: 10~15분)

1. 실습 개요

본 실습에서는 "/Assistants/function_calling/assistants_function_calling_with_bing_search.ipynb" 코드를 이해하고 실행해 봅니다.

assistants_function_calling_with_bing_search.ipynb 노트북은 [Bing Search APIs](https://www.microsoft.com/bing/apis/llm) and [function calling](https://learn.microsoft.com/azure/ai-services/openai/how-to/function-calling?tabs=python)와 [function calling](https://learn.microsoft.com/azure/ai-services/openai/how-to/function-calling?tabs=python)을 이용하여 Azure OpenAI 모델을 웹 데이터로부터 그라운드 하는 방법을 보여줍니다. 이 방법은 모델이 웹에서 검색하는 최신 데이터에 접근하는 좋은 방안입니다.

이 샘플은 Function Calling 기능과 서치 기반의 그라운딩을 배우려고 하는 개발자들과 데이터 과학자들에게 유용할 것 입니다.

2. 학습 목표

이 실습의 주요 목적은 향상된 기능을 위해 LLM에서 Assistants Function Calling을 어떻게 사용하는지 보여주는 것 입니다.

이 실습에서 사용된 서비스:

- Azure OpenAI Service - you can apply for access [here](https://go.microsoft.com/fwlink/?linkid=2222006)
- Bing Search - [Bing Search resource](https://learn.microsoft.com/en-us/bing/search-apis/bing-web-search/create-bing-search-service-resource) 

3. 프로그래밍 언어

- Python

### Assistants File Search 구현 해 보기 (예상 소요시간: 10~15분)

1. 실습 개요

본 실습에서는 "/Assistants/function_calling/assistants_file_search.ipynb" 코드를 이해하고 실행해 봅니다.

assistants_file_search.ipynb 노트북은 [Azure OpenAI Assistants (preview)의 File Search 툴](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/file-search?tabs=python)을 이용하여 Azure OpenAI 모델 외부의 지식 (제품 정보, 사용자 제공 문서등)으로 Assistant를 보강하는 방법을 보여줍니다. OpenAI는 문서를 자동으로 구문 분석 및 청킹하고, 임베딩을 생성 및 저장하고, 벡터 및 키워드 검색을 모두 사용하여 관련 콘텐츠를 검색하여 사용자 쿼리에 답변합니다.

2. 학습 목표

이 실습의 주요 목적은 향상된 기능을 위해 LLM에서 Assistants File Search를 어떻게 사용하는지 보여주는 것 입니다.

이 실습에서 사용된 서비스:

- Azure OpenAI Service - you can apply for access [here](https://go.microsoft.com/fwlink/?linkid=2222006)

3. 프로그래밍 언어

- Python

## 실습 순서

* [Azure OpenAI Assistants Function Calling, File Search 사용해보기](https://github.com/jeongaelee/ProjectMooModule1/blob/main/Assistants.md)
* [Azure OpenAI On Your Data - File Upload](https://github.com/jeongaelee/ProjectMooModule1/blob/main/OnYourData-FileUpload.md)
* [Azure OpenAI On Your Data - Embeddings and Search](https://github.com/jeongaelee/ProjectMooModule1/blob/main/OnYourData-EmbeddingsAndSearch.md)

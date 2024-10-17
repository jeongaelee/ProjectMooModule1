# Azure OpenAI On Your Data - Part2

Azure OpenAI의 On Your Data는 OpenAI 모델의 파워를 활용할 수 있으며, Azure의 엔터프라이즈급 보안을 적용하여 데이터에 직접 RAG 모델의 고급 기능을 통합 할 수 있습니다. 이 기능을 사용하여, 연결하고 상호 작용하고, 자체 데이터를 그라운드하는 방식을 좀 더 정확하고 빠르게 변화시킬 수 있습니다. 자체 데이터로 개인화된 Copilot을 빠르게 생성하여 사용자의 이해를 높이고, 작업을 신속하게 완료하며, 의사 결정을 도울 수 있습니다.

## 2. Wiki 데이터를 Azure OpenAI 서비스로 임베딩 및 문서 검색

Embedding은 머신 러닝 학습 모델 및 알고리즘에서 쉽게 활용할 수 있는 특수한 형식의 데이터 표현입니다. Embedding은 텍스트 데이터를 고차원 벡터로 변환하여 기계 학습 모델이 언어의 의미를 이해하고 처리할 수 있도록 돕는 기술입니다. 본 실습에서는 Azure OpenAI의 Text Embedding 모델을 통하여 Wiki 데이터를 변환하고, Azure Storage에 해당 데이터를 업로드한 후, Azure AI Search에서 Index를 생성하여 검색에 활용하는 것을 실습합니다.

### Azure OpenAI에서 임베딩 모델 Deployment
1. Azure OpenAI Studio에서 텍스트 임베딩 모델을 배포합니다. "Deployment" 메뉴에서 "+Deploy model"을 클릭하고 "Deploy base model"을 선택합니다.

    <img src="images/embeddings01.png" width="500"/>

2. 본 실습에서는 text-embedding-ada-002 (Version 2)를 사용합니다. text-embedding으로 검색하여 모델을 선택 후 "Confirm"합니다.

    <img src="images/embeddings02.png" width="500"/>

3. 입력 항목들을 기본값으로 입력한 후 "Deploy"합니다.

    <img src="images/embeddings03.png" width="500"/>

### Azure OpenAI의 text embedding 모델을 활용하여 Wiki 데이터 Embedding
4. "Embeddings" 폴더 아래의 "embedding_wiki.ipynb" 파이썬 노트북을 열어 Wiki 데이터의 embedding을 진행합니다.

    <img src="images/embeddings04.png" width="300"/>

### Azure Storage에 embedding된 콘텐츠가 포함 된 파일 업로드
5. embedding_wiki.ipynb을 성공적으로 실행하였다면, "Embeddings/data" 폴더 안에 "wiki_data_embeddings.csv" 파일이 생성되었을 것입니다. 이 파일은 wiki 데이터를 Azure OpenAI의 text embedding 모델을 통하여 벡터로 변환한 "content_vector" 열이 추가되었습니다. 이 파일을 Azure Blob Storage에 업로드 하기 위하여 Azure Portal을 열어 이전 과정에서 생성한 Storage Account로 이동합니다.

    Storage Account의 "Data storage" 메뉴 아래의 "Containers"를 선택하고 "+ Container"를 클릭하여 "wikidatasource-01"이라는 새로운 Container를 생성합니다.

    <img src="images/embeddings05.png" width="700"/>

6. Container에 "wiki_data_embeddings.csv" 파일을 업로드 합니다.

    <img src="images/embeddings06.png" width="700"/>

### Azure AI Search에서 Index 생성하기
7. Azure Portal에서 Azure AI Search 리소스를 열어 wiki-index-01라는 Index를 생성합니다. "Search management" 메뉴에서 "Indexes"를 선택하고 "+ Add Index"를 클릭합니다.

    <img src="images/embeddings07.png" width="500"/>

8. "wiki_data_embeddings.csv" 파일은 'id', 'url', 'title', 'text', 'n_tokens', 'content_vector'의 여섯개 열이 포함 되어있습니다. 각각의 Field를 추가하고, Type등의 값을 정해줍니다. 

    'content_vector'를 제외하고는 모두 "String" 타입으로 선택합니다.
    
    url, title과 text는 Retrievable에 체크해 줍니다.

    title과 text는 Searchable 데이터로, "Searchable" 항목을 활성화 합니다. 영문 데이터이기 때문에 Analyzer는 기본값인 "Standard - Lucene"으로 선택하면 됩니다.

    'content_vector'는 벡터 데이터이므로 "Collection(Edm.Single)"을 선택합니다. Dimensions는 "1536"으로 입력합니다. Vector Search Profile을 생성하기 위하여 "Create"를 클릭합니다.

    <img src="images/embeddings08.png" width="800"/>

9. 'content_vector'의 "Algorithms" 설정을 생성합니다.

    "Vector algorithm"은 Kind: "exhaustiveKnn", Similarity metric: "cosine"으로 선택합니다.

    <img src="images/embeddings09.png" width="300"/>

10. 'content_vector'의 "Vectorizers" 설정을 생성합니다.

    <img src="images/embeddings10.png" width="300"/>

11. Index 이름을 "wiki-index-01"로 입력한 후 Index를 생성합니다.

    <img src="images/embeddings11.png" width="800"/>

### Azure AI Search에서 Data Source 생성하기

12. Azure AI Search에 wiki-datasource-01라는 Data source를 생성합니다. "Search management" 메뉴에서 "Data sources"를 선택하고 "+ Add data source"를 클릭합니다.

    <img src="images/embeddings12.png" width="500"/>

13. Add data source의 각 항목에 아래와 같이 입력합니다. 이름을 "wiki-datasource-01"이라고 입력하고, 위의 단계에서 생성했던 Blob container를 선택한 후, data source를 생성합니다.

    <img src="images/embeddings13.png" width="500"/>

### Azure AI Search에서 Indexer 생성하기

14. Azure AI Search에 wiki-indexer-01라는 Indexer를 생성합니다. "Search management" 메뉴에서 "Indexer"를 선택하고 "+ Add indexer"를 클릭합니다. "Add Indexer (JSON)" 옵션을 선택합니다. (Note: UI로 Indexer를 생성하는 메뉴에 delimitedText partsingMode가 정상 동작하지 않아서, 본 실습에서는 JSON으로 생성하도록 하겠습니다.)

    "Add indexer (JSON)"의 입력란에 아래의 JSON을 복사하여 붙여넣기 하고 저장합니다.

```
    {
        "name": "wiki-indexer-01",
        "dataSourceName": "wiki-datasource-01",
        "targetIndexName": "wiki-index-01",
        "parameters": {
            "configuration": {
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true
            }
        }
    }
```

<img src="images/embeddings14.png" width="800"/>

15. Indexer가 성공적으로 생성된 것을 확인합니다. 53열의 데이터가 입력된 것을 확인합니다.

    <img src="images/embeddings15.png" width="700"/>

16. (몇분이 지난 후) Indexes 메뉴에서 Indexing이 완료되면, Document count가 53으로 변한 것을 확인합니다.

    <img src="images/embeddings16.png" width="800"/>

17. wiki-index-01을 선택하여 상세 페이지를 확인합니다. Search 입력창에 아무것도 입력하지 않고 "Search"를 하면, Index를 생성할때 Retrievable에 체크했던 Field가 Display되는 것을 보실 수 있습니다.

    <img src="images/embeddings17.png" width="800"/>

### Azure OpenAI Studio에서 Azure AI Search의 데이터를 이용하여 채팅 완성

18. Azure OpenAI Studio를 열어 Chat Playground를 선택합니다. "Add your data" 탭에서, "+Add a data source"를 클릭합니다.

    <img src="images/embeddings18.png" width="600"/>

19. 데이터 소스에 "Azure AI Search"를 선택합니다. Azure AI Search Index에는 이전 단계에서 생성한 "wiki-index-01"을 선택합니다.

    <img src="images/embeddings19.png" width="600"/>

20. Search type을 "Hybrid (vector + keyword)"로 선택합니다.

    <img src="images/embeddings20.png" width="600"/>

21. 인증 방식을 "API Key"로 선택합니다.

    <img src="images/embeddings21.png" width="600"/>

22. 선택한 내용을 확인하고 "Save and close"를 클릭합니다.

    <img src="images/embeddings22.png" width="600"/>

23. Chat Playground에서 Data source가 Azure AI Search의 wiki-index-01 index로 선택되었는지 확인한 후, "4월과 8월을 비교하는 표를 만들어줘."라고 프롬프트를 입력합니다. 

    결과에 4월과 8월에 관한 표가 응답되고, 아래의 Citation에 Wiki 문서의 관련 내용이 reference로 나와있는 것을 확인합니다.

    <img src="images/embeddings23.png" width="800"/>

## 실습 순서

* [Global Batch API](https://github.com/jeongaelee/ProjectMooModule1/blob/main/Batch.md)
* [Azure OpenAI Assistants Function Calling, File Search 사용해보기](https://github.com/jeongaelee/ProjectMooModule1/blob/main/Assistants.md)
* [RAG를 사용한 Python 채팅 샘플 애플리케이션](https://github.com/jeongaelee/ProjectMooModule1/blob/main/RAG.md)
* [Azure OpenAI On Your Data - File Upload](https://github.com/jeongaelee/ProjectMooModule1/blob/main/OnYourData-FileUpload.md)
* [Azure OpenAI On Your Data - Embeddings and Search](https://github.com/jeongaelee/ProjectMooModule1/blob/main/OnYourData-EmbeddingsAndSearch.md)

# Hackthon Bemobi 2024

## Ideia: PayCycle

**Explicação**:

O **PayCycle** é um Gestor Proativo de Assinaturas é uma solução baseada em Inteligência Artificial Generativa (GenAI) projetada para otimizar a experiência dos clientes de serviços baseados em assinaturas com pagamentos recorrentes. A ferramenta monitora os ciclos de pagamento, envia notificações sobre cobranças futuras, recomenda atualizações de planos com base no uso e sugere novos serviços ou descontos em caso de sinais de possível cancelamento.

**Ferramentas**:

- **Python**: Linguagem principal para análise de dados e machine learning.
- **Apache Airflow**: Automação de tarefas e orquestração de fluxos de trabalho.
- **Pandas**: Para análise e manipulação de dados.
- **TensorFlow ou PyTorch**: Construção de modelos preditivos.
- **PostgreSQL**: Banco de dados relacional para armazenar dados de clientes e assinaturas.

**Funcionalidades**:

- **Monitoramento proativo**: Acompanha o ciclo de vida das assinaturas e notifica os usuários sobre cobranças e renovações.
- **Recomendações personalizadas**: Sugere atualizações de plano ou novos serviços com base no comportamento do cliente.
- **Prevenção de cancelamentos**: Detecta sinais de churn e oferece promoções ou descontos para reter o cliente.

**Estrutura do Projeto**:

```bash
gestor-proativo-assinaturas/
├── dags/                    # DAGs do Airflow para orquestração
├── data/                    # Dados simulados para testes
├── models/                  # Modelos de Machine Learning
├── notebooks/               # Jupyter Notebooks para desenvolvimento e testes
├── scripts/                 # Scripts Python para automação
├── Dockerfile               # Arquivo Docker para containerização do projeto
├── docker-compose.yml       # Arquivo Docker Compose para orquestrar múltiplos serviços
└── README.md                # Documentação do projeto
```

Para testar e desenvolver o **PayCycle**, você precisará de um ambiente que permita integrar diferentes componentes, realizar testes contínuos e garantir que tudo funcione de forma orquestrada. Aqui estão as opções e etapas para montar um ambiente de teste eficiente:

### 1. **Escolha do Ambiente de Desenvolvimento**

-  **Local**: Pode ser uma boa opção para desenvolvimento inicial, com as ferramentas instaladas no seu computador.
	- **Docker**: O Docker facilita a configuração de ambientes de desenvolvimento e teste consistentes, pois você pode criar containers para suas ferramentas (Airflow, TensorFlow, Pandas) e rodar tudo de maneira isolada.
	- **Virtualenv ou Conda**: Para isolar dependências de Python e garantir que todas as bibliotecas estejam corretamente instaladas.
- **Nuvem**: Utilizar plataformas de nuvem facilita a escalabilidade e o trabalho em equipe, além de fornecer suporte para testar com grandes volumes de dados. Algumas opções incluem Google Cloud, AWS e Azure.
	- **Google Cloud Platform (GCP)**:
	  - **BigQuery**: para armazenar e consultar dados;
	  - **AI Platform**: para treinar e hospedar modelos de machine learning.
	- **Amazon Web Services (AWS)**:
	  - **Sagemaker**: para treinamento e deploy de modelos de machine learning;
	  - **AWS Lambda**: para automação de pequenas tarefas.
	- **Microsoft Azure**:
	  - **Azure Machine Learning**: para construção e deploy de modelos de ML;
	  - **Azure Logic Apps**: para orquestração de workflows.

### 2. **Configuração do Ambiente Local**

**A. Instalação e Configuração do Docker**

Para garantir um ambiente consistente, o Docker é útil para rodar cada parte do sistema isoladamente. Você pode criar containers para o Apache Airflow, o TensorFlow e o Pandas.

Exemplo de Docker Compose:
```yaml
version: '3'
services:
  airflow:
    image: puckel/docker-airflow
    environment:
      - LOAD_EX=n
      - EXECUTOR=LocalExecutor
    volumes:
      - ./dags:/usr/local/airflow/dags
    ports:
      - "8080:8080"
    depends_on:
      - database

  database:
    image: postgres
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
    ports:
      - "5432:5432"

  tensorflow:
    image: tensorflow/tensorflow:latest
    ports:
      - "8888:8888"
    volumes:
      - ./model:/app/model
    command: "jupyter notebook --ip=0.0.0.0 --allow-root"
```

Esse exemplo cria containers para o **Airflow**, um banco de dados Postgres e um ambiente de **TensorFlow** com Jupyter Notebook para testes e modelagem.

**B. Instalação e Configuração do Virtualenv**

Se preferir rodar localmente, crie um ambiente isolado usando `virtualenv` ou `conda`:
```bash
# Instalar o virtualenv
pip install virtualenv

# Criar o ambiente
virtualenv gestor_env

# Ativar o ambiente
source gestor_env/bin/activate

# Instalar dependências
pip install pandas tensorflow apache-airflow
```

### 3. **Automatização de Testes e Fluxos**

Para rodar testes de integração, você pode configurar fluxos no Airflow, além de testar o modelo preditivo em notebooks de TensorFlow.

**A.  Testar o Modelo de Machine Learning**

No Jupyter Notebook ou Python Script:

- Carregue um **conjunto de dados de teste** que simule os ciclos de pagamento e comportamento de clientes.
- Execute o modelo de machine learning para fazer previsões sobre a probabilidade de churn.

```python
# Teste com dados simulados
test_data = pd.DataFrame({
    'subscription_duration': [10, 15, 3],  # em meses
    'failed_payments': [0, 1, 3],
    'usage_pattern': [80, 50, 20]  # Porcentagem de uso do serviço
})

# Previsão com o modelo já treinado
predictions = model.predict(test_data)
print(predictions)
```

**B. Testar a Automação com Apache Airflow**

No Airflow, crie uma **DAG de teste** para simular o processo de monitoramento e notificação.

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

def test_notification():
    print("Notificação de cobrança enviada!")

# Definir a DAG
dag = DAG('test_subscription_management', start_date=datetime(2024, 10, 1), schedule_interval='@daily')

# Definir a tarefa
notify_task = PythonOperator(
    task_id='send_notification',
    python_callable=test_notification,
    dag=dag,
)
```

Rodar localmente via `airflow webserver` e `airflow scheduler` permitirá testar se o fluxo de monitoramento e notificação está funcionando.

### 4. **Ambiente de Testes na Nuvem**

Se preferir rodar na nuvem, pode utilizar ambientes gerenciados como:

- **GCP** para rodar BigQuery e AI Platform:
  - Crie um ambiente Jupyter ou use o Google Colab para treinar modelos com TensorFlow.
  - Use o **Cloud Functions** ou **Cloud Composer** (orquestrador de tarefas baseado em Airflow) para automatizar os fluxos.
  
- **AWS** para rodar Lambda Functions:
  - Crie um ambiente com **Sagemaker** para treinamento de modelos e deploy.
  - Use **AWS Lambda** para executar scripts automáticos de notificação ou predição em horários específicos.

### 5. **Testes de Integração e Desempenho**

Testar a **integração** entre seus componentes é crucial:
- Verifique se o **modelo de machine learning** está gerando as previsões corretas.
- Teste se o **Airflow** consegue programar corretamente as notificações e enviar os alertas de cobrança ou de upgrade de plano.
- Faça testes de carga para ver se o sistema consegue lidar com grandes volumes de assinaturas e usuários.

### Resumo dos Próximos Passos:

1. **Configuração do ambiente**: Escolha entre local ou na nuvem e instale as ferramentas (Docker, Virtualenv, etc.).
2. **Desenvolvimento de modelos e automação**: Crie scripts de machine learning e fluxos de trabalho automatizados com Airflow.
3. **Testes contínuos**: Use dados simulados para testar os modelos e automatizar as previsões.
4. **Deploy em ambientes de produção**: Após os testes locais, faça o deploy em um ambiente de nuvem (AWS, GCP, etc.) para rodar em escala.

## Como Rodar o Projeto

**Requisitos:**
- Docker
- Docker Compose
- Python 3.8+
- Pip

**Passos para Executar:**

1. Clone o repositório:
 ```bash
   git clone https://github.com/seu-usuario/gestor-proativo-assinaturas.git
   cd gestor-proativo-assinaturas
   ```
   
2. Construa e inicie os containers com Docker:
 ```bash
   docker-compose up --build
   ```
   
3. Acesse a interface do Airflow:
   - O Airflow estará disponível em [http://localhost:8080](http://localhost:8080).
   
4. Teste o modelo de machine learning:
   - Acesse o notebook de treinamento em `notebooks/model_training.ipynb`.

### Dados:
Os dados simulados para teste estão disponíveis na pasta `data/`. Você pode adicionar novos conjuntos de dados conforme necessário para aprimorar o modelo. Além disso, você pode utilizar o [Google Colab](https://colab.research.google.com/drive/1raQRJty59dEHEifdXkdQsq3OxLiA2kVL?authuser=0#scrollTo=fRcbVDhXe3Bh) para testar e experimentar com os modelos de machine learning. Basta fazer upload dos arquivos de dados da pasta `data/` para o Colab e executar os notebooks disponíveis na pasta `notebooks/`.

**Contribuindo:**
Contribuições são bem-vindas! Por favor, crie um **fork** deste repositório e abra um **pull request** com as melhorias ou correções.

**Licença**:
Este projeto está sob a licença MIT - veja o arquivo [LICENSE](LICENSE) para mais detalhes.

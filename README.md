![cover_image](https://github.com/krmdel/medical_assistant_rag/blob/main/Images/cover_image.jpeg)
Source: DALL-E 3

# **Medical Assistant Using Retrieval-Augmented Generation (RAG)**
**Table of Contents**
- **Problem Description**
- **Why This Tool is Useful in the Real World**
- **Features Developed**
- **Installing Pipenv and Dependencies**
- **Elasticsearch Setup**
- **Using Docker Compose**
  - **Build and Run**
  - **Stopping Services**
  - **Switching Between Streamlit and Gunicorn**
- **Using Dockerfile Directly**
  - **Build the Docker Image**
  - **Run the Docker Container**
  - **Stop and Remove the Container**
- **Accessing Services**
- **Conclusion**
- **Notes**


- **Problem Description**
  
The healthcare industry faces the challenge of managing and extracting meaningful information from vast repositories of medical data. Physicians, medical students, and patients frequently seek answers to complex medical questions, ranging from symptom evaluation to disease diagnosis. However, finding accurate and relevant information in a timely manner can be difficult due to the volume and complexity of medical literature, research papers, clinical guidelines, and case reports.

Additionally, the variability in medical knowledge sources makes it challenging to provide precise, contextually relevant responses. As a result, there is a pressing need for intelligent systems that can retrieve accurate information and present it in a coherent and user-friendly manner.

- **Why This Tool is Useful in the Real World?**
  
A Retrieval-Augmented Generation (RAG) medical assistant addresses these challenges by combining the power of large language models (LLMs) with advanced retrieval mechanisms.

  - **Enhance Decision-Making:** Healthcare professionals can quickly retrieve pertinent information for clinical decision-making, reducing the time spent searching through medical texts.
  - **Support Patients:** It provides patients with reliable and accessible health information, improving health literacy and empowering them to make informed decisions about their care.
  - **Save Time and Resources:** Automating information retrieval and generation helps medical practitioners and support staff streamline their workflow, improving efficiency in patient care.
  - **Facilitate Research and Education:** Medical students and researchers can access a vast repository of knowledge, staying updated with the latest medical research and best practices.

 ![RAG](https://github.com/krmdel/medical_assistant_rag/blob/main/Images/RAG.png)

**Features Developed**

- **Data Conversion and Indexing for Efficient Retrieval**
  
The raw data from a medical Q&A dataset was transformed to include key fields for indexing and fast retrieval. By structuring the data, the system efficiently searches and provides precise responses to user queries, ensuring accuracy and relevance.
Data Source: Jin, Di, et al. "What Disease does this Patient Have? A Large-scale Open Domain Question Answering Dataset from Medical Exams." arXiv preprint arXiv:2009.13081 (2020), GitHub: https://github.com/jind11/MedQA

- **Synthetic Question Generation for Testing RAG Performance**
  
Synthetic questions were generated based on the original Q&A dataset to test the retrieval performance of the RAG system. This approach allows for comprehensive testing and fine-tuning, ensuring the model performs well across a variety of real-world scenarios and question formats.

- **Hybrid Search with Re-ranking Using Elasticsearch**
  
Elasticsearch was used for indexing and searching, combining both text-based and vector-based search with document re-ranking. This hybrid approach significantly improves retrieval accuracy, surfacing the most relevant documents for a given query.

- **Retrieval Evaluation Metrics: MRR and Hit Rate**
  
Retrieval performance was measured using key metrics such as Mean Reciprocal Rank (MRR) and Hit Rate. These metrics provide a quantitative evaluation of the system's retrieval efficiency, guiding further improvements.

  Before parameter fine-tuning as baseline:
  Hit Rate: 96.76%,
  MRR: 90.60%. 
  
  After parameter fine-tuning for boosting:
  Hit Rate: 96.76%,
  MRR: 90.81%

- **LLM-as-a-Judge for RAG Evaluation**
  
An "LLM-as-a-judge" approach was employed to evaluate the responses generated by the RAG system. This method uses a separate language model to assess the relevance and quality of the retrieved information, enhancing reliability and accuracy.

  GPT-4o-mini:
  Relevant           91.11%
  Partly Relevant    4.44%
  Non-Relevant       4.44%
      
  GPT-4o:
  Relevant           64.75&
  Partly Relevant    21.63%
  Non-Relevant       10.61%

- **Monitoring and Metrics via Grafana**
  
A comprehensive monitoring dashboard was implemented using Grafana to track key metrics such as user feedback, relevance of responses, response time, cost, and token usage. This enables continuous monitoring and optimization of the system's performance.

![grafana_dashboard](https://github.com/krmdel/medical_assistant_rag/blob/main/Images/grafana_dashboard.png)

- **User Interface and API Access**
  
A user-friendly Streamlit UI was developed to allow users to interact with the assistant and provide feedback. Alternatively, the system can be accessed as an API endpoint using Gunicorn, making it adaptable for integration with other applications.

![streamlit_1](https://github.com/krmdel/medical_assistant_rag/blob/main/Images/streamlit_1.png)

- **Automatic Storage of RAG Results and User Feedback on AWS S3**
  
The results generated by the RAG model, along with user feedback, are automatically stored in AWS S3. This ensures that all interactions are securely logged, enabling continuous learning, model improvement, and insights into user behavior.

![s3_bucket](https://github.com/krmdel/medical_assistant_rag/blob/main/Images/s3_bucket.png)

**Installing Pipenv and Dependencies**

Install Pipenv (if not already installed):

```bash
pip install pipenv
```
Install Project Dependencies:

```bash
pipenv install
```

**Elasticsearch Setup**

Before running the application, start an Elasticsearch container:

```bash
docker run -it \
    --rm \
    --name elasticsearch \
    -m 2GB \
    -p 9200:9200 \
    -p 9300:9300 \
    -e "discovery.type=single-node" \
    -e "xpack.security.enabled=false" \
    docker.elastic.co/elasticsearch/elasticsearch:8.9.0
```

**Using Docker Compose: Build and Run**

To build and run the services using Docker Compose, open a terminal, navigate to the directory containing the docker-compose.yml, and run:

```bash
docker-compose up --build
```

This command will:

- Read the .env file for environment variables.
- Build the Docker images.
- Start all services (Postgres, app, Grafana).
  
**Stopping Services**

To stop all running services, use:

```bash
docker-compose down
```

**Switching Between Streamlit and Gunicorn**

Modify the APP_TYPE in your .env file (APP_TYPE=streamlit or APP_TYPE=gunicorn).

**Restart the services**

```bash
docker-compose down
docker-compose up --build
```

**Using Dockerfile Directly**

Build the Docker Image
Run the following command in the terminal to build the Docker image:

```bash
docker build -t my_app_image .
```
Replace my_app_image with your preferred image name.

**Run the Docker Container**

Run the Docker container with the necessary environment variables:

```bash
docker run -p 8501:8501 -p 5000:5000 \
  -e APP_TYPE=streamlit \  # Change to "gunicorn" if needed
  -e OPENAI_API_KEY=your_api_key_here \
  -e DATA_PATH=data/data_metadata_small.csv \
  --name my_app_container my_app_image
```

Replace my_app_image with the name of the image you built.
Replace my_app_container with a name for your container.

**Stop and Remove the Container**

To stop the running container:

```bash
docker stop my_app_container
```
To remove the container:

```bash
docker rm my_app_container
```

**Accessing Services**

Streamlit: 
Go to http://localhost:8501 in your web browser if APP_TYPE=streamlit.

Gunicorn: 
Go to http://localhost:5000 if APP_TYPE=gunicorn. Update the question in test.py to send the request

```bash
python test.py
```

Grafana: Access Grafana at http://localhost:3300.

**Conclusion**

The Medical Assistant Using Retrieval-Augmented Generation (RAG) project combines LLMs with efficient search mechanisms to tackle the complex challenge of providing accurate and relevant medical information. By leveraging Elasticsearch for hybrid search, this system can become highly scalable, reliable, and adaptable for various healthcare applications.

However, since the system utilizes a general-purpose language model trained on a broad range of general knowledge, there is a significant risk of domain-specific failures. Medical data is inherently more specialized, and deviations in the generated answers may arise if the model’s foundational training lacks comprehensive coverage of intricate medical details. This could lead to inaccurate or less precise responses in scenarios where deep medical expertise is required. Additionally, there is a potential risk that subtle but critical medical nuances may be missed or misinterpreted. Therefore, there is a huge room for improvement, for instance, deploying a fine-tuned model on domain specific data for RAG.

To mitigate these risks, continuous fine-tuning of the language model on specific medical datasets, as well as leveraging domain-specific medical LLMs, will be essential. Incorporating real-world clinical data and user feedback loops will also help ensure that the system evolves to meet the high standards expected in medical practice.

As this project continues to evolve, future developments can include fine-tuning the language model, improving retrieval accuracy, and expanding the scope of datasets to cover a broader range of medical knowledge. Additionally, the integration of real-world clinical data will be essential to ensure that the system remains at the cutting edge of medical decision support.

This tool brings potential benefit to streamline information retrieval in healthcare, saving time and resources while improving decision-making and patient outcomes.

**Notes**

Replace the placeholders in the .env file with your own values.
Use Docker Compose for managing multiple services like databases and monitoring tools.
If you encounter any issues, check the logs for each service to identify the problem.

Feel free to raise issues or contribute to this project.

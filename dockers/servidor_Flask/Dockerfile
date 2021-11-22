FROM python:3.7
COPY practica_big_data_2019 /practica_big_data_2019
ENV PROJECT_HOME=/practica_big_data_2019
WORKDIR /practica_big_data_2019
RUN pip install -r requirements.txt
WORKDIR /practica_big_data_2019/resources/web
ENTRYPOINT ["python3","predict_flask.py"]
EXPOSE 3000
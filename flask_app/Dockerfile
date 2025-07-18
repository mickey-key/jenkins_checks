FROM python:3.14.0b4-alpine3.22
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
EXPOSE 8080
ENTRYPOINT ["flask"]
ENV FLASK_APP=main.py
CMD ["run", "--host=0.0.0.0", "--port=8080"]

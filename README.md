flask-mysql-app/
│
├── app/
│ ├── app.py
│ ├── requirements.txt
│
├── Dockerfile

Create Flask App
app/app.py:

from flask import Flask
import mysql.connector
app = Flask(__name__)
@app.route(&#39;/&#39;)
def index():
    try:
        conn = mysql.connector.connect(
            host=&#39;mysql-container&#39;,  # Will match the MySQL container name
            user=&#39;root&#39;,
            password=&#39;example&#39;,
            database=&#39;testdb&#39;
        )

        cursor = conn.cursor()
        cursor.execute(&quot;SELECT NOW();&quot;)
        result = cursor.fetchone()
        return f&quot;Connected to MySQL! Current time: {result[0]}&quot;
    except mysql.connector.Error as err:
        return f&quot;Error: {err}&quot;

Create Requirements File
app/requirements.txt:

flask
mysql-connector-python

 Create Dockerfile
Dockerfile:
FROM python:3.11-slim
WORKDIR /app
COPY app/ .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 5000
CMD [&quot;python&quot;, &quot;app.py&quot;]

Create Docker Network

docker network create flasknet

Run MySQL Container
docker run --name mysql-container --network flasknet \
  -e MYSQL_ROOT_PASSWORD=example \
  -e MYSQL_DATABASE=testdb \
  -p 3306:3306 \
  -d mysql:8.0

Build Flask App Image
docker build -t flask-mysql-app .

Run Flask App Container
docker run --name flask-app --network flasknet -p 5000:5000 flask-mysql-app

 Access the App
Open your browser and go to:
http://localhost:5000

# cloud_opss
* 1° Instalar o python 2.7 ou superior
* 2° Importar as Libs  

Nesse caso vamos utlizar: 
---

  * Flask
  * Flask-SQLAlchemy
  * Flask-Restful
  * Jsonify 

Para baixar essas libs basta executar no terminal o seguinte comando:

~~~
   $  pip install flask flask-jsonpify flask-sqlalchemy flask-restful
~~~

* 3° Utilize SQLITE
* Segue link para acessar o BD - [BAIXAR](https://github.com/robson17/cloud_opss/blob/main/case_cloudopss.db)

O nome do arquivo de banco de dados é case_cloudopss.db que ficará na raiz do seu projeto.

* Recuperar o arquivo de banco e utilizar o flask
db_connect = create_engine('sqlite:///case_cloudopss.db')
app = Flask(__name__)
api = Api(app)

* Criar os endpoints de User, GET, POST E PUT

~~~python
class Users(Resource):
    def get(self):
        conn = db_connect.connect()
        query = conn.execute("select * from user")
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)
        
    def post(self):
        conn = db_connect.connect()
        name = request.json['name']
        email = request.json['email']
        tel = request.json['tel']
        end = request.json['end']
        prof = request.json['prof']

        conn.execute(
            "insert into user values(null, '{0}','{1}')".format(name, email,tel,end,prof))

        query = conn.execute('select * from user order by id desc limit 1')
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)
        
    def put(self):
        conn = db_connect.connect()
        id = request.json['id']
        name = request.json['name']
        email = request.json['email']
        tel = request.json['tel']
        end = request.json['end']
        prof = request.json['prof']

        conn.execute("update user set name ='" + str(name) + "', email ='" +
                     str(email) + "', tel ='" + str(tel) + "',end ='" + str(end) +
                     "',prof ='" + str(prof)+"' where id =%d " % int(id))
    query = conn.execute("select * from user where id=%d " % int(id))
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)
        
* Criar os endpoints de envio de ID, com os métodos GET e DELETE

 ~~~python
class UserById(Resource):
    def delete(self, id):
        conn = db_connect.connect()
        conn.execute("delete from user where id=%d " % int(id))
        return {"status": "success"}
    def get(self, id):
        conn = db_connect.connect()
        query = conn.execute("select * from user where id =%d " % int(id))
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)
~~~

* Mapear os endpoints criados e finalizar o arquivo
~~~python
api.add_resource(Users, '/users') 
api.add_resource(UserById, '/users/<id>') 
if __name__ == '__main__':
    app.run()
~~~


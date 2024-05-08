# Backend Flask with Login google create Tasks

<!-- #### [Same Project with React](https://github.com/diegoperea20/Flask-React-Login-create-Tasks)  -->

<p align="justify">
Backend Flask for Nextjs fronted with next auth with credentials and google , with Flask REST API with sqlalchemy mysql where there is a login and register (you can change password and email, delete account) where each user can create titles and descriptions (you can edit and delete them) and in 'same' section the user can select the count of how many people have the same title he has created and see the emails of those people who have the same title.
</p>

<p align="center">
  <img src="README-images/logingoogle.PNG" alt="Step1">
</p>

<p align="justify">
First createan account  without google where you must create a username, password ("Must include at least one number.", "Must include at least one lowercase letter,"Must include at least one lowercase letter.", "Must include at least one uppercase letter.", "Must include at least one uppercase letter.","Must include at least one uppercase letter.", "Must include at least one uppercase letter.", "Must include at least one uppercase letter.","Must include at least one special character.", "Must include at least one special character.", "Must include at least one special character.","The length of the password must be equal to or greater than 8 characters.","Must not contain blank spaces.")  Confirm your password and enter an email address.
</p>

<p align="center">
  <img src="README-images/loginup_1.PNG" alt="Step2">
</p>

<p align="justify">
After entering the data correctly, click on the "Register" button.
</p>

<p align="center">
  <img src="README-images/loginup-2.PNG" alt="Step3">
</p>



<p align="justify">
In login enter your username and password, click on the "Login" button.
</p>

<p align="center">
  <img src="README-images/logingoogle.PNG" alt="Step4">
</p>


<p align="justify">
After logging in you will see the home screen where you will see your user name and registration id, in this section you can log out, change your password, delete your account (delete your account and tasks), and create a new task.
</p>

<p align="center">
  <img src="README-images/home.PNG" alt="Step5">
</p>

<p align="justify">
In change password you can change your password and email if required.
</p>

<p align="center">
  <img src="README-images/changepassword.PNG" alt="Step6">
</p>

<p align="justify">
In task, you can create a title and a description of your choice, you can edit and delete it, and with the "home" button you go back to home and the "same" button to the same section.
</p>

<p align="center">
  <img src="README-images/task-1.PNG" alt="Step7">
</p>

<p align="justify">
Once you have created your task you can edit it with the "Edit" button where you will see the title and description, you only have to modify it by clicking on the "Update" button. If you don't want to make the modification click on the "Cancel Edit" button.
</p>

<p align="center">
  <img src="README-images/task-edit.PNG" alt="Step8">
</p>
<p align="center">
  <img src="README-images/task-edit-2.PNG" alt="Step9">
</p>
<p align="center">
  <img src="README-images/task-edit-3.PNG" alt="Step10">
</p>

<p align="justify">
In this same section there are three buttons in which "Task" to return to Task, the button "Count People Same titile" once clicked counts how many users have the same title of the titles that the user has created in his account and these are shown in a table, the button "People Emails same title" once clicked shows the emails of the users that have the same title that the user has created in his account in a table form.
</p>

<p align="center">
  <img src="README-images/same.PNG" alt="Step11">
</p>

<p align="justify">
If there are no matching titles, a message will appear where it says: No title matches with other users.
</p>

<p align="center">
  <img src="README-images/same-not.PNG" alt="Step12">
</p>

<p align="justify">
But if there are the same titles, the table of the selected button appears, in this case "Count People Same title":
</p>
<p align="center">
  <img src="README-images/same-count.PNG" alt="Step13">
</p>

<p align="justify">
But if there are the same titles, the table of the selected button appears, in this case "People Emails same title":
</p>
<p align="center">
  <img src="README-images/same-emails.PNG" alt="Step14">
</p>


## Steps to implement it
Backend Options for do it: 

1. Use Dockerfile or docker-compose.yml
2. Use virtual enviroments and apply  requirements.txt 
```python
pip install -r requirements.txt
```
Use Docker 
```python
#Comands for use docker container mysql
docker run --name mymysql -e MYSQL_ROOT_PASSWORD=mypassword -p 3306:3306 -d mysql:latest

docker exec -it mymysql bash

#Inside of the container mysql
mysql -u root -p

create database flaskmysql;

```
[Backend without  google authorization link](https://github.com/diegoperea20/Flask-React-Login-create-Tasks/tree/main/Backend)


Addeds changes in Backend python for google authorization in comparation with [Backend without  google authorization link](https://github.com/diegoperea20/Flask-React-Login-create-Tasks/tree/main/Backend) 

```python
@app.route('/', methods=['POST'])
def login():
    data = request.get_json()
    username = data['user']
    password = data['password']

    user = User.query.filter_by(user=username).first()
    if user and check_password_hash(user.password, password):
       
        token = generate_token(user)  

        #added username for response authentication
        return jsonify({'token': token ,"user_id": user.id , "username": user.user}), 200

    return jsonify({'error': 'Credenciales inválidas'}), 401

#---google-pasword-random
import random
import string

def generate_random_password():
    length = random.randint(8, 16)  # Genera una longitud aleatoria entre 8 y 16 caracteres

    # Define los caracteres válidos para cada categoría
    letters_lower = string.ascii_lowercase
    letters_upper = string.ascii_uppercase
    digits = string.digits
    special_chars = string.punctuation

    # Asegura que la contraseña contenga al menos un carácter de cada categoría
    password = random.choice(letters_upper) + random.choice(letters_lower) + random.choice(digits) + random.choice(special_chars)

    # Completa la contraseña con caracteres aleatorios
    password += ''.join(random.choices(string.ascii_letters + string.digits + string.punctuation, k=length-4))

    # Mezcla los caracteres de la contraseña
    password = ''.join(random.sample(password, len(password)))

    #Convierte la contraseña en hash
    password = generate_password_hash(password)

    return password
#----GOOGLE
from google.oauth2 import id_token
from google.auth.transport import requests

#----GOOGLE-LOGIN-AND-LOGIN -UP
@app.route('/logingoogle', methods=['POST'])
def google_authentication():
    token = request.json.get('token')

    if token:
        try:
            id_info = id_token.verify_oauth2_token(token, requests.Request())
            email = id_info.get('email')
            user = id_info.get('name')
            
            # Verificar si el usuario ya existe en tu base de datos
            existing_user = User.query.filter_by(email=email).first()
            if existing_user:
                # Inicio de sesión
                token_in = generate_token(existing_user)  # Generar token de autenticación
                return jsonify({'token': token_in, 'user_id': existing_user.id, 'username': existing_user.user}), 200
            else:
                # Creación de nuevo usuario
                password = generate_random_password()
                new_user = User(email, user, password)
                db.session.add(new_user)
                db.session.commit()
                return user_schema.jsonify(new_user), 201
        except ValueError:
            return jsonify({'error': 'Invalid token'}), 401
    else:
        return jsonify({'error': 'Token not provided'}), 400

```
# LAB: Reliable Google Cloud Infrastructure: DevOps Automation 

## Objectives

In this lab, you will learn how to perform the following tasks:

	- Create a Git repository

	- Create a simple Python application

	- Test Your web application in Cloud Shell

	- Define a Docker build

	- Manage Docker images with Cloud Build and Container Registry

	- Automate builds with triggers

	- Test your build changes

## Steps

1. Create a Git Repository

	- Enter this command to create repository:

		gcloud source repos create devops-repo

	- Create a folder called gcp-course with this command:

		mkdir gcp-course

	- Change to the folder you just created:

		cd gcp-course

	- Clone the empty repository you just created:

		gcloud source repos clone devops-repo

	- The previous command created an empty folder called devops-repo. Change to that folder:

		cd devops-repo

2. Create a Simple Python Application

	- Enster in the *devops-repo* repository you juste cloned:

		cd ~/gcp-course/devops-repo

	- Create a new file named *main.py* with nano text editor:

		nano main.py

	- In the nano text editor window, paste the following text:

		"from flask import Flask, render_template, request

		 app = Flask(__name__)

		 @app.route("/")
		 def main():
    	 model = {"title": "Hello DevOps Fans."}
    	 return render_template('index.html', model=model)

		if __name__ == "__main__":
    	app.run(host='0.0.0.0', port=8080, debug=True, threaded=True)"

    - Save your changes and quit the text editor:

    	[Do not copy, keyboard combination]

    	ctrl + o

    	ctrl + x

    - Create a new folder called *templates*:

    	mkdir templates

    - Enter into the templates directory you just created:

    	cd templates

    - Add a new file called *layout.html* 

    	nano layout.html

    - Add the following code and save the file as you did before:

    	"<!doctype html>
		<html lang="en">
			<head>
    			<title>{{model.title}}</title>
    			<!-- Bootstrap CSS -->
    			<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css">

			</head>
			<body>
    			<div class="container">

        			{% block content %}{% endblock %}

        		<footer></footer>
    			</div>
			</body>
		</html>"

		[Do not copy, keyboard combination]

		ctrl + o

		ctrl + x

	- Add new file called *index.html*

		nano index.html

	- Add the following code and save the file as you did before:

		"{% extends "layout.html" %}
		{% block content %}
		<div class="jumbotron">
    		<div class="container">
        		<h1>{{model.title}}</h1>
    		</div>
		</div>
		{% endblock %}"

	- In Python, application prerequisites are managed using pip. Now you will add a file that lists the requirements for this application. In the devops-repo folder (not the templates folder), create a file called requirements.txt

		nano requirements.txt

	- Add the following to that file and save it:

		Flask==1.1.1

		[Do not copy, keyboard combination]

		ctl + o

		ctrl + x

	- You have some files now, so save them to the repository. First, you need to add all the files you created to your local Git repo. In Cloud Shell, enter the following code:

		cd ~/gcp-course/devops-repo

		git add --all

	- To commit changes to the repository, you have to identify yourself. Enter the following commands, but with your information (you can just use your Gmail address or any other email address):

		git config --global user.email "you@example.com"

		git config --global user.name "Your Name"

	- Now, commit the changes locally:

		git commit -a -m "Initial Commit"

	- You committed the changes locally, but have not updated the Git repository you created in Cloud Source Repositories. Enter the following command to push your changes to the cloud:

		git push origin master

3. Test Your Web Application in Cloud Shell

	- In Cloud Shell, make sure you are in your application's root folder, and then install the Flask framework using pip:

		cd ~/gcp-course/devops-repo

		sudo pip3 install -r requirements.txt

	- To run the program, type:

		python3 main.py

	- To see the program running, run this command:

		gcloud app browse

	- To stop the program, return to the Cloud Console and press Ctrl+C in Cloud Shell.

	- Enter into the *devops-repo* directory and then open the *main.py* file with the nano text editor:

		cd ~/gcp-course/devops-repo

		nano main.py

	- In the main() function, change the title to something else (whatever you want)

	- save your changes and quit the text editor:

		press ctrl + o

		press ctrl + x

	- In the Cloud Shell window at the bottom, commit your changes using the following commands:

		cd ~/gcp-course/devops-repo

		git commit -a -m "Second Commit"

	-Push your changes to the cloud using the following command:

		git push origin master

4. Define a Docker Build

	- Enter into the *devops-repo* directory and then open the *Dockerfile* file with the nano text editor:

		cd ~/gcp-course/devops-repo

		nano Dockerfile

	**The file Dockerfile is used to define how the container is built.**

	- At the top of the file, enter the following:

		FROM python:3.7

	**This is the base image. You could choose many base images. In this case, you are using one with Python already installed on it.**

	- Enter the following:

		WORKDIR /app

		COPY . .

	- Enter the following:

		RUN pip install gunicorn

		RUN pip install -r requirements.txt

	**This uses pip to install the requirements of the Python application into the container. Gunicorn is a Python web server that will be used to run the web app.**

	- Enter the following:

		ENV PORT=80

		CMD exec gunicorn --bind :$PORT --workers 1 --threads 8 main:app

	**The environment variable sets the port that the application will run on (in this case, 80). The last line runs the web app using the gunicorn web server.**

	- Verify that the completed file looks as follows and save it:

		FROM python:3.7

		WORKDIR /app

		COPY . .

		RUN pip install gunicorn

		RUN pip install -r requirements.txt

		ENV PORT=80

		CMD exec gunicorn --bind :$PORT --workers 1 --threads 8 main:app

5. Manage Docker Images with Cloud Build and Container Registry

	- Return to Cloud Shell. Make sure you are in the right folder:

		cd ~/gcp-course/devops-repo

	- The Cloud Shell environment variable DEVSHELL_PROJECT_ID automatically has your current project ID stored. The project ID is required to store images in Container Registry. Enter the following command to view your project ID:

		echo $DEVSHELL_PROJECT_ID

	- Enter the following command to use Cloud Build to build your image:

		gcloud builds submit --tag gcr.io/$DEVSHELL_PROJECT_ID/devops-image:v0.1 .

	**Notice the environment variable in the command. The image will be stored in Container Registry.**

	- If asked to enable Cloud Build in your project, type Yes. Wait for the build to complete successfully.

	- Run the following command, your image should be on the list:

		gcloud container images list

	- You will now try running this image from a Compute Engine virtual machine.

		gcloud compute instances create-with-container [your_vm_name] \
    --container-image gcr.io/<your-project-id-here>/devops-image:v0.1 (change the project ID where indicated)

    - Once the VM starts, create a browser tab and make a request to this new VM's external IP address. The program should work as before.

    *You might have to wait a minute or so after the VM is created for the Docker container to start.*

    - You will now save your changes to your Git repository. In Cloud Shell, enter the following to make sure you are in the right folder and add your new Dockerfile to Git:

    	cd ~/gcp-course/devops-repo

		git add --all

	- Commit your changes locally:

		git commit -am "Added Docker Support"

	- Push your changes to Cloud Source Repositories:

		git push origin master

6. Automate Builds with Triggers

	- create a trigger:

		gcloud beta builds triggers create cloud-source-repositories --repo=devops-repo --branch-pattern=".*" --build-config=Dockerfile --name=devops-trigger
	
	- To test your trigger, run :

	- List your container registry contents again, you should see a new folder, devops-repo, with a new image in it:

		gcloud container images list

	- Return into the Cloud Shell. Enter in the gcp-course/devops-repo folder:

		cd ~/gcp-course/devops-repo

	- Open the *main.py* file:

		nano main.py

	- In the main() function, Change the title property to "Hello Build Trigger."

	- Commit the change with the following command:

		git commit -a -m "Testing Build Trigger"

	- Enter the following to push your changes to Cloud Source Repositories:

		git push origin master

7. Test Your Build Changes

	- To view your builds, run the following command:

		gcloud builds list

	- To view details about a specific build, run the following command:

		gcloud builds describe [BUILD_ID]

	- Copy the image link

	- As you did earlier, create a new virtual machine to test this image. 

		gcloud compute instances create-with-container [your_vm_name] \
    --container-image [paste image link that you just copied]

    - When the machine is created, test your change by making a request to the VM's external IP address in your browser. Your new message should be displayed.

    **You might have to wait a few minutes after the VM is created for the Docker container to start.**
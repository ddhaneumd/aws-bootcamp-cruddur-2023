FROM python:3.10-slim-buster

# make a new folder inside container
WORKDIR /backend-flask

#going from outside container to inside of the container
# contains libraries which we want to install to run the application
COPY requirements.txt requirements.txt

#install python libraries used for the app
RUN pip3 install -r requirements.txt

#going from outside container to inside of the container
# here "." means everything in the current directory. That is first "." means
# everything in /backend-flask (outside container) and second "." means everything in /backend-flask (inside container)
COPY . .

#set Environment Variables (Env Vars)
# inside container and will remain set when the container is running
ENV FLASK_ENV=development

EXPOSE ${PORT}
#command to run the app
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
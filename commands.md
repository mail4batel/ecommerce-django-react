git add .
git commit -m "batel3"
git push origin main

vagrant ssh jenkins-app
sudo systemctl status jenkins
sudo systemctl restart jenkins



docker build -t mail4batel/djangoweb:tag .
docker push mail4batel/djangoweb:tag

git config --global user.email "mail4batel@gmail.com"
git config --global user.name "mail4batel"

*for the report--
deshbord -> manage jenkins -> script console 
System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "")

10.0.2.15:80
172.17.0.1:80

#build automatic--
configure -> Build periodically ->
H/2 * * * *

#before running the pipeline:
edit /frontend/src/screens/HomeScreen.js (Latest Products)

#to do after the build happens to see the website--
docker pull mail4batel/app-frontend
docker run -p 8081:80 mail4batel/app-frontend
in browser open localhost:8081
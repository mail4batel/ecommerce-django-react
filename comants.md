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

System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "")

10.0.2.15:80
172.17.0.1:80
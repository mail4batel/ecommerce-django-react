git add .
git commit -m "batel3"
git push origin main

vagrant ssh jenkins-app
sudo systemctl status jenkins

docker build -t mail4batel/djangoweb:tag .
docker push mail4batel/djangoweb:tag

git config --global user.email "mail4batel@gmail.com"
git config --global user.name "mail4batel"
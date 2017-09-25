```
brew install jenkins
# brew install jenkins-lts

jenkins --httpPort=8080
# jenkins script runs the .war and is in /usr/local/Cellar/jenkins/<version>/bin

# admin user password is printed to log and ~/.jenkins/secrets/initialAdminPassword

# login at localhost:8080
# given option to install suggested plugins or select plugins to install
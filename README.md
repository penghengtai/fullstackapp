For the article of this repo, please refer to: https://www.swtestacademy.com/deploy-full-stack-application-in-kubernetes/

## INSTRUCTIONS

### Create database image

Navigate to postgress folder and type:

```bash
docker build -t fullstackappdb .
docker save fullstackappdb:latest > db.tar
sudo chown root:root db.tar
sudo mv db.tar /var/lib/rancher/k3s/agent/images

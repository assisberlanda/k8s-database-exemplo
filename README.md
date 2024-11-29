# ☸️ Kubernetes
### Para excluir o Load Balancer e Criar um em YAML
 	kubectl get service
  	kubectl delete service app-html
### Arquivo YAML
| Pod | apiVersion: v1 |
|-|-|
| Deployment | apiVersion: app/v1 |
| Service    | apiVersion: v1     |
 Crie um app-html-lb.yml
 ## ❗️ Arquivo YAML para Load Balancer
	apiVersion: v1
	kind: Service
	metadata: 
  	  name: app-html-lb
	spec:
  	  selector:
    	    app: app-html
  	  ports:
    	    - port: 80
      	      targetPort: 80
 	  type: LoadBalancer
# Executar bash dentro do pod
	kubectl get pod
	kubectl exec --stdin --tty myapp-php -- /bin/bash
 ### Pod com Service no mesmo arquivo YAML
 => somente colocar --- dividindo o arquivo.
 
 	apiVersion: v1
	kind: Pod
	metadata:
 	 name: myapp-php
 	 labels:
 	   app: myapp-php
	spec:
	  containers:
	  - name: myapp-php
	    image: assisberlanda/myapp-php:1.0
	    ports:
	    - containerPort: 80

	---

	apiVersion: v1
	kind: Service
	metadata:
	  name: myapp-php-service
	spec:
 	 type: NodePort
 	 selector:
  	  app: myapp-php
 	 ports:
   	 - port: 80
   	   targetPort: 80
    	   nodePort: 30005
## Encaminhamento de porta
	kubectl port-forward pod/mysql-pod 3306:3306
## 🌎 Google Cloud
#### Desabilitar Firewall da porta para o Deployment
	 gcloud compute firewall-rules list
 	gcloud compute firewall-rules create backend --allow tcp:30005
#### Rodar o arquivo index.html do frontend
    kubectl get service
    minikube service myapp-php-service --url
- pega a url e cola no arquivo js.js e abra em um novo browser
#### Para acessar o Pod de Banco de Dados
    kubectl get pod
    kubectl exec --tty --stdin mysql-5b9495fcf6-4f82q -- /bin/bash
    mysql -u root -h 127.0.0.1 -p
    use meubanco;
    SELECT * FROM mensagens;
### 📌 Para parar um Pod 📌
    kubectl scale deployment php --replicas=0
    kubectl scale deployment mysql --replicas=0
## 🧩 Persistencia dos Dados Local
-Arquivo YAML [mysql-local.yml](https://academiapme-my.sharepoint.com/personal/kawan_dio_me/_layouts/15/onedrive.aspx?id=%2Fpersonal%2Fkawan%5Fdio%5Fme%2FDocuments%2FSlides%20dos%20Cursos%2FKubernetes%2FM%C3%B3dulo%203%2FCurso%202%2Fb%2E%20Armazenamento%20de%20dados%20local%2Etxt&parent=%2Fpersonal%2Fkawan%5Fdio%5Fme%2FDocuments%2FSlides%20dos%20Cursos%2FKubernetes%2FM%C3%B3dulo%203%2FCurso%202&ga=1)
- Cria o arquivo YAML faz o Deploy;
- Acessa o Pod e cria a tabela meubanco;
- Insere dados nela.

  		mkdir volumes
  		cd volumes
  		code .
  		// mysql-local.yml
  		kubectl apply -f ./mysql-local.yml
  		kubectl get pods
  		kubectl exec --tty --stdin mysql-699c9467c-6vkbg -- /bin/bash
  		mysql -u root -h http://127.0.0.1 -p
  		// Digite a senha
  		use meu banco
  		// Cria a tabela e insere dados <Arquivo YAML>
  		SELECT * FROM mensagens
  		exit
  		exit
  ## Usando pv.yml
  		apiVersion: v1
		kind: PersistentVolume
		metadata:
 		 name: local
  		labels:
   		 type: local
		spec:
 		 storageClassName: manual
  		capacity:
   		 storage: 100Mi
  		accessModes:
    		- ReadWriteOnce
  		hostPath: 
    		path: /meubanco/
  ## Usando pvc.yml
  		apiVersion: v1
		kind: PersistentVolumeClaim
		metadata:
 		 name: local
		spec:
  		storageClassName: manual
 		 accessModes:
   		 - ReadWriteOnce
  		resources:
   		 requests:
  		  storage: 100Mi
💡 Daí deve mudar o msql-local.yml (somente a parte final no volumes)

	volumes:
      	  - name: local
          persistentVolumeClaim:
            claimName: local
	    
# 🌎 Provisionar PersistentVolumes dinamicamente
## Google Cloud Plataform - [GCP](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes?hl=pt-br#dynamic_provisioning)
## Kubernetes - [k8s](https://kubernetes.io/pt-br/docs/concepts/storage/persistent-volumes/)
### ☁️ nos Cloud Providers as persistencias de dados se dão pelo [NFS](https://debian-handbook.info/browse/pt-BR/stable/sect.nfs-file-server.html) 

- No GCP - Filestore

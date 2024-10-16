 # Para excluir o Load Balancer e Criar um em YAML
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
    
✅🆑💡❇️📌🧩☁️🌎

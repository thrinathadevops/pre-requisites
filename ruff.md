if we use front end and backend each require defferent type of capacity.

 we have to know how to build the product , if we have thousand file wecannot use like that, the devops which developer commiting to git is a raw file. how we can convert it has a product/drlivrablr/artifact (machine readable format), this process is called as software builds.
 what happen internally, each file in the sorce code, we convert into machine readable format, this process will do with compilation, it will genearte machine redable file called object files, then object files will combine and generate the executable file, this process is called as software builds.
 so the above process little bit old, as with technolagy the software aslo eavolving, it divide in compiled and interpreted it means it will run line by line. 
interpritor language  mainly used for froentend services, comiple language used for backend (application layer) services. 

what is the process to build run etc, we will see for java essentials means what whata re the requirement for compiler language.

Build we require javac (compiler executable) jar.exe 
Run we require java (runtime executable) java.exe jre (runtime environment) 

with jdk (development kit) we can build and run. with is we will get output is .jar or .war . for java we require heap memory.

the maven structure is src it contains sub folders like main and test in main we have java files and resources, in test we have test java files and resources unit test against main files. we have config file pom.xml how to compile and run the unit test and which file have to generate. the object files class file in target folder unit test it put in separate folder reports and jar file put in target folder.
 hear build means compile, unit test and package


to skip anything for example to skip unit test we can use skipTests=true mvn clean package -Dmaven.test.skip=true
first it will download dependencies it will keep it in .m2 folder

mv clean it delete entire target folder, this we will called as a full build. 

in interprter 
node essentials

install npm node package manager
run nodejs
deliverable .zip .tar
build stepss there is no compolation, unit test using a framework, create a tar/zip with all the necessary *.js/*.html

build process install, unit test, package 

package.json is a configuration file it contains dependencies, scripts, version, name etc

npm install it will download dependencies it will keep it in node_modules folder

software deployemt
where do we want to run the application . few example
install the application separate machine for qa prod install on whichever machine we want
configuration the application it has to connect db we tell where is the data base means details of db and certifications 
starting the application
comibining these theree points called as deployment.

for application configuration we have to provide 
log levels: info, war, error, debug, trace for production we use error for qa we use info but require qa change to debug or trace.
|USERDB|PASSWORD|LOG_LEVEL|DB_HOST|DB_PORT|DB_NAME|DB_CERT|Log-file|Cerificates|Heap-Memory
Log Files: server.log / access.log / users.log 

for java application.properties is the configuration of application. for nodejs we have to create a myserver.config file
if we keep in when in package then not require but in reality we don't keep it in package so we have keep this file when running the application. 

npm ci --production it will install only production dependencies


infrastructure as code 

why we nee it, by doing manual it is very difficulty means provisioning will be slow, difficult to scale. 

desired state is what we need.  
actual state what is actually in the cloud

so we prepare a desired state and we will get actual state by using code. 
what terrafoem do there are few commands like 
terraform init it will initialize the terraform download dependencies like providers in (.terraform folder), plugins etc and backend , it will create a statefile.
terraform validate it will validate the code like syntax errors and it will check the code is compatable.
terraform plan it will show the actual state and desired state difference meance what we write in the code .tf file it will compare with with actual state present in the statefile, it won't connect directly to the cloud and it shows us we are adding the infrastructure  or destroying the infrastructure.

terraform apply it will connect to the cloud and it will create the infrastructure, as per the desired state.

terraform destroy it will destroy the infrastructure, as per the desired state. while destroying we use traget to destroy the specific resource.

we change dynamicallly when plan or appying using terraform plan --varaible="key=value" during run time can change the value 


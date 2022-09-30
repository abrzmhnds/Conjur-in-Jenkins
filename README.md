### Install Conjur

1. Pull the Docker images

   images defined in `docker-compose.yml`:
   ```
   docker-compose pull
   ```

2. Generate the master key
   ```
   docker-compose run --no-deps --rm conjur data-key generate > data_key
   ```

3. Load master key as an environment variable

   Load `data_key` file content (the master data key) as an environment variable:
   ```
   export CONJUR_DATA_KEY="$(< data_key)"
   ```

4. Start the Conjur Open Source environment

   ```
   docker-compose up -d
   ```

5. Create an admin account
   Create a Conjur account and initialize the built-in admin user.
   ```
   docker-compose exec conjur conjurctl account create myConjurAccount > admin_data
   ```

6. Connect the Conjur client to the Conjur server
   Use the account name that you created in step 5:
   ```
   docker-compose exec client conjur init -u conjur -a myConjurAccount
   ```

### Define Policy and Variable

1. Log in to Conjur as admin
   Log in to Conjur as admin. When prompted for a password, insert the API key
   stored in the `admin_data` file:
   ```
   docker-compose exec client conjur authn login -u admin
   ```

2. Load the sample policy

   Load the provided sample policy into Conjur built-in `root` policy to create
   the resources for the Jenkins. 

   Load jenkins-host. Here to see how to setup jenkins host with docker nginx reverse proxy https://gitlab.com/abriza_mahandis/docker-nginx-reverse-proxy.git 
   ```
   docker-compose exec client conjur policy load root policy/jenkins-host.yml > jenkins-host
   ```

   Load jenkins-var
   ```
   docker-compose exec client conjur policy load root policy/jenkins-var.yml > jenkins-var
   ```

   In case you want to replce or update
   ```
   docker-compose exec client conjur policy load --replace root policy/jenkins-var.yml > jenkins-var
   docker-compose exec client conjur policy load --update root policy/jenkins-var.yml > jenkins-var
   ```

3. Store a Variable

   ```
   conjur variable values add <policy-path-of-variable-name> <secret-value>
   ```
   example
   ```
   conjur variable values add jenkins-host/variablename assword123
   ```
   check variable
   ```
   conjur variable value jenkins-host/variablename
   ```

**Setup in Jenkins: https://www.conjur.org/blog/adding-conjur-secrets-management-to-your-jenkins-pipeline/**

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

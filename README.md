# Notes

1.  Trust DotNet Dev https cert

    The following command will create a self-signed localhost certificate and setup the
    cert to be trusted

          dotnet dev-certs https -t

2.  Create Docker File

    Create the docker file for the project, then run the following command

          docker build -t <projectName> .

3.  Run Docker image

    Run the docker image next, by using the following command

        docker run -p <External_Port>:<internal_port> <projectname>

        ex: docker run -p 8080:80 <project_name>

    - Don't forget the port mapping or you will not be able to access the project because
      the port will not be accessible from the container.

4.  Create Development Certificate for Container

    - To create the development certificate to be used in your container run the following
      command _ IMPORTANT: MAKE SURE YOU RUN THIS COMMAND FROM YOUR PROJECT DIRECTORY _

            dotnet dev-certs https -ep $env:USERPROFILE\.aspnet\https\<ProjectName>.pfx -p pa55word!

    - After running the command, check the following location to ensure the cert was
      created

            C:\%USERPROFILE%\.aspnet\https

5.  Creating User Secrets

    - User secrets are placed in a settings file in your local environment and are meant
      for development purposes only

    - When you create a user secret a file is placed in the following path :

            C:\Users\<username>\AppData\Roaming\Microsoft\UserSecrets\<id>

    - To bind your the secrets.json file to your project, you must add a property to your
      .csproj file under the propertyGroup node called UserSecretsId. The value here must
      be a unique identifier and must match the identifier used when you create the
      secret. ex:

            <PropertyGroup>
                <UserSecretsId>dda25df4-9a88-4a7e-8502-2134b74e4729</UserSecretsId>
            </PropertyGroup>

    - A good pattern to follow is to prefix the unique identifier with the app name, as
      this makes it easier to find the folder.

    - Below is a list of useful dotnet secret commands to know :

            This command will list the keys and values from the secrets.json file
            dotnet user-secrets list


            This command is used to set a value to a specified key:
            dotnet user-secrets set "AppSettings:ConnectionString" "some-value"


            This command is used to remove a key/value pair
            dotnet user-secrets remove "AppSettings:ConnectionString"

      - For this project create the UserSecretsId in your .csproj file

      - Next, create the user secret and make sure you are in the project directory when
        you create the user secret because it will use the Id specified above to bind your
        key:value pair for your project.

            dotnet user-secrets set "Kestrel:Certificates:Development:Password" "pa55w0rd!"

      - Using the key Kestrel:Certificates:Development:Password, allows you to avoid
        having to add code to find the value. When the key is read in by the .net core
        configuration api it will know exactly what key is for because of the
        path/structure that is provided.

6.  After adding the user secret, remember to rebuild your docker image to ensure the
    secret is included in the image.

        You can do this by running the following command:

            docker build -t <project_name> .

7.  Test your project by running docker run

    We are going to run the following command:

        docker run -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_ENVIRONMENT=Development -v $env:APPDATA\microsoft\UserSecrets\:/root/.microsoft/usersecrets -v $env:USERPROFILE\.aspnet\https:/root/.aspnet/https/ <name_of_image>

8.  Create docker compose file

9. Run docker-compose up build

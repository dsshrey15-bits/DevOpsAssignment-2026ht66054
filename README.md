# BITS Pilani Student Fitness API

This project implements a Flask-based fitness and wellness API for a BITS Pilani student context, along with CI/CD automation using GitHub Actions and a Jenkins pipeline example.

## Project overview

The application exposes a minimal API that supports:
- checking the status of the service
- listing available fitness programs
- calculating estimated daily calories for a client
- generating a client summary for a chosen training plan

## Screenshot walkthrough

The [`Screenshots/`](Screenshots/) folder records the setup and CI/CD work in sequence. Each screenshot is embedded below its step for context.

1. **Set up SSH access and clone the repository.** Register your public SSH key with GitHub, then clone the repository to the development machine.
   ![Set up SSH access and clone the repository](Screenshots/Screenshot_2026-10-07_00-31-15.png)

2. **Stage and commit the initial application.** Review the new repository files, stage the application, and record the initial commit.
   ![Stage and commit the initial application](Screenshots/Screenshot_2026-10-07_00-40-13.png)

3. **Push the initial commit to GitHub.** Publish the initial project commit to the remote repository.
   ![Push the initial commit to GitHub](Screenshots/Screenshot_2026-10-07_00-49-26.png)

4. **Start a test branch and add the test scaffold.** Create a feature branch for testing and add the initial test file under `tests`.
   ![Start a test branch and add the test scaffold](Screenshots/Screenshot_2026-10-07_00-56-03.png)

5. **Switch to the CI/CD branch and add the Jenkinsfile.** Create a separate branch for CI/CD work and add a Jenkins pipeline definition to the project.
   ![Switch to the CI/CD branch and add the Jenkinsfile](Screenshots/Screenshot_2026-10-07_00-58-50.png)

6. **Build the Docker image.** Build the application image from the project Dockerfile.
   ![Build the Docker image](Screenshots/Screenshot_2026-10-07_01-05-19.png)

7. **Push the Jenkins pipeline changes.** Publish the branch containing the Jenkins pipeline so it can be used by the CI server.
   ![Push the Jenkins pipeline changes](Screenshots/Screenshot_2026-10-07_01-08-47.png)

8. **Publish the test branch.** Set up the remote tracking branch so the test branch can be shared and updated.
   ![Publish the test branch and set its upstream](Screenshots/Screenshot_2026-10-07_01-12-37.png)

9. **Run the container and check the API.** Start the application in a Docker container and verify that the programs endpoint returns JSON.
   ![Run the container and check the API](Screenshots/Screenshot_2026-10-07_01-14-02.png)

10. **Add GitHub credentials in Jenkins.** Use **Manage Jenkins → Credentials → System → Global → Add Credentials**. Store the GitHub username and token/password as a Jenkins credential; do not put the secret in the repository or README.
    ![Add GitHub credentials in Jenkins](Screenshots/Screenshot_2026-10-07_01-16-16.png)

11. **Configure the Jenkins pipeline source.** In the job configuration, select the Git repository, choose the saved Jenkins credential, and set the branch specifier to `*/main`.
    ![Configure the Jenkins pipeline source](Screenshots/Screenshot_2026-10-07_01-21-46.png)

12. **Create the Jenkins pipeline job.** Choose **New Item**, enter a job name, select **Pipeline**, and continue to its configuration.
    ![Create the Jenkins pipeline job](Screenshots/Screenshot_2026-10-07_01-23-45.png)

13. **Verify the running API.** Confirm the application responds with the available fitness programs.
    ![Verify the running API from the shell](Screenshots/Screenshot_2026-10-07_01-24-41.png)

14. **Confirm the Jenkins credential is saved.** The credential appears in the Global credentials list and can now be selected in the job configuration.
    ![Confirm the Jenkins credential is saved](Screenshots/Screenshot_2026-10-07_01-29-27.png)

15. **Create a GitHub personal access token for Jenkins.** Create the token in GitHub Developer Settings with only the access Jenkins needs, then save it directly as a Jenkins credential. Copy the token when GitHub displays it; do not commit or share it.
    ![Create a GitHub personal access token for Jenkins](Screenshots/Screenshot_2026-10-07_01-31-02.png)

16. **Merge the CI/CD work into the main branch.** Bring the completed pipeline work into the main development branch and publish the update.
    ![Merge the CI/CD work into main and push](Screenshots/Screenshot_2026-10-07_01-46-05.png)

17. **Check the GitHub Actions run.** The screenshot shows the workflow completing its setup, dependency installation, syntax check, tests, and Docker image build successfully.
    ![Check the GitHub Actions run](Screenshots/Screenshot_2026-10-07_02-09-15.png)

18. **Check the Jenkins build.** The Jenkins pipeline screenshot shows successful checkout, Python setup, tests, and Docker image build stages.
    ![Check the Jenkins build](Screenshots/Screenshot_2026-10-07_02-09-44.png)

19. **Publish the GitHub Actions workflow.** Add the workflow to the repository and push it to `main`; this triggers GitHub Actions. The successful run shown above is a captured run, so check the repository's **Actions** tab for the result of the latest update.
    ![Commit and push the GitHub Actions workflow](Screenshots/Screenshot_2026-10-07_02-10-03.png)

> **Reading the captures:** Browser tabs and terminal windows may show different working sessions. Follow each step's description rather than assuming every adjacent screenshot is the same uninterrupted session.

## Local setup

1. Clone or open the project folder.
2. Create a virtual environment:
   ```bash
   python -m venv .venv
   . .venv/bin/activate
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Run the Flask app:
   ```bash
   python app.py
   ```
5. Check the API health endpoint at:
   ```text
   http://localhost:5000/
   ```

## Manual testing

Run the test suite with:

```bash
pytest -q
```

## Docker

Build the Docker image:

```bash
docker build -t aceest-app .
```

Run the Docker container:

```bash
docker run -p 5000:5000 aceest-app
```

## GitHub Actions workflow

The GitHub Actions pipeline defined in `.github/workflows/main.yml` runs on every push and pull request. It performs the following steps:
- checks out the repository
- installs Python dependencies
- validates syntax using `compileall`
- runs the Pytest suite
- builds the Docker image

## Jenkins integration

The `Jenkinsfile` demonstrates how the build can be automated in Jenkins with the following stages:
- checkout source code
- install dependencies
- run tests
- build Docker image

This gives a second validation layer alongside GitHub Actions in a CI/CD environment.

## Key endpoints

- `GET /` - service health check
- `GET /programs` - available programs
- `POST /calculate-calories` - calculate calories
- `POST /client-summary` - generate a client summary

Example payload for calorie calculation:

```json
{
  "program": "Fat Loss",
  "weight_kg": 70
}
```

Example response:

```json
{
  "program": "Fat Loss",
  "weight_kg": 70,
  "calories_per_day": 1540
}
```

# SciPy CI/CD Pipeline - Step-by-Step Tutorial
Src: Claude

## 📋 Prerequisites

Before starting, ensure you have:
- Docker installed and running
- Git installed
- GitHub account
- Docker Hub account (optional, for registry)
- Text editor/IDE (VS Code, PyCharm, etc.)

## 🚀 Step 1: Project Setup

### 1.1 Create Project Directory
```bash
mkdir scipy-cicd-project
cd scipy-cicd-project
```

### 1.2 Initialize Git Repository
```bash
git init
git branch -M main    # rename the current branch's name to 'main'
```

### 1.3 Create Project Structure
```bash
# Create directories
mkdir -p src tests .github/workflows

# Create empty files
touch main.py
touch src/__init__.py
touch src/data_processor.py
touch tests/__init__.py
touch tests/conftest.py
touch tests/test_data_processor.py
touch requirements.txt
touch Dockerfile
touch docker-compose.yml
touch Makefile
touch .github/workflows/ci-cd.yml
touch .gitignore
touch .env
```

### 1.4 Create .gitignore
```bash
cat > .gitignore << 'EOF'
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/
.coverage
htmlcov/
.pytest_cache/
.DS_Store
*.png
*.jpg
*.jpeg
EOF
```

## 🐳 Step 2: Docker Configuration

### 2.1 Copy the Dockerfile
Copy the Dockerfile content from the artifacts provided earlier into your `Dockerfile`.

### 2.2 Copy requirements.txt
Copy the requirements.txt content from the artifacts into your `requirements.txt`.

### 2.3 Copy docker-compose.yml
Copy the docker-compose.yml content from the artifacts into your `docker-compose.yml`.

### 2.4 Test Docker Setup
```bash
# Build the Docker image
docker build -t scipy-app:test .

# Verify image was created
docker images | grep scipy-app
```

## 💻 Step 3: Application Code

### 3.1 Create Main Application
Copy the sample SciPy project code from the artifacts into:
- `src/data_processor.py`
- `main.py`

### 3.2 Create Test Suite
Copy the test suite code from the artifacts into:
- `tests/test_data_processor.py`
- `tests/conftest.py`

### 3.3 Test Application Locally
```bash
# Test the application runs
python main.py

# Should output statistical analysis results
```

## 🔧 Step 4: Local Development Environment

### 4.1 Copy Makefile
Copy the Makefile content from the artifacts into your `Makefile`.

### 4.2 Test Local Commands
```bash
# Build Docker image
make build

# Run tests
make test

# Run linting
make lint

# Run all quality checks
make quality

# Start development environment
make dev
```

### 4.3 Verify Development Environment
```bash
# Check running containers
docker ps

# Should show app, jupyter, and test services
```

## 🔄 Step 5: GitHub Actions CI/CD

### 5.1 Copy GitHub Actions Workflow
Copy the GitHub Actions workflow from the artifacts into `.github/workflows/ci-cd.yml`.

### 5.2 Create GitHub Repository
```bash
# Create repository on GitHub (via web interface)
# Then connect local repository:

git remote add origin https://github.com/YOUR_USERNAME/scipy-cicd-project.git
```

### 5.3 Enable GitHub Container Registry and Test `ghcr`
- Go to your GitHub repo
- Settings → Actions → General
- Scroll to "Workflow permisssions"
- Select "Read and write permissions"
- Save changes
- Account Settings → Developer Settings → Tokens (classic) → Classic tokens
- Click Generate new token
  - Name: `ghcr-push-token
  - Scopes:
    - `write:packages`
    - `read:packages`
    - `delete:packages`
    - `repo` (for a private repo)
- Save the Username and Token in `.env` file
- Test `ghcr`
```bash
# login
make login-ghcr

# build and tag image
make build
docker tag scipy-app:latest ghcr.io/<username>/scipy-cicd-project:latest

# push to ghcr
docker push ghcr.io/<username>/scipy-cicd-project:latest
```
- Go to GitHub → Profile → Packages: where the pushed Docker image will be listed here

### 5.4 Initial Commit and Push
```bash
git add .
git commit -m "Initial commit: SciPy CI/CD pipeline setup"
git push -u origin main
```

## 🧪 Step 6: Test the CI/CD Pipeline

### 6.1 Verify Pipeline Triggered
- Go to GitHub → Actions tab
- You should see a workflow run triggered by your push
- Monitor the pipeline execution

### 6.2 Check Pipeline Steps
The pipeline should execute:
1. ✅ Build Docker image
2. ✅ Run tests in container
3. ✅ Run linting
4. ✅ Run code formatting check
5. ✅ Build and push to registry (if main branch)
6. ✅ Deploy step (placeholder)

### 6.3 Verify GitHub Container Registry
If pipeline succeeds:
- Check GitHub Profile → Packages, for your pushed image
- Image should be tagged as `latest` and with commit SHA

## 🌿 Step 7: Feature Development Workflow

### 7.1 Create Feature Branch
```bash
git checkout -b feature/new-analysis-function
```

### 7.2 Add New Feature
Add this function to `src/data_processor.py`:
```python
def advanced_statistics(self):
    """Advanced statistical analysis"""
    if self.data is None:
        raise ValueError("No data loaded")
    
    from scipy import stats
    
    # Perform additional statistical tests
    result = {
        'skewness_x': stats.skew(self.data['x']),
        'kurtosis_x': stats.kurtosis(self.data['x']),
        'skewness_y': stats.skew(self.data['y']),
        'kurtosis_y': stats.kurtosis(self.data['y'])
    }
    
    return result
```

### 7.3 Add Test for New Feature
Add this test to `tests/test_data_processor.py`:
```python
def test_advanced_statistics(self):
    """Test advanced statistical analysis"""
    self.processor.load_data()
    stats = self.processor.advanced_statistics()
    
    required_keys = ['skewness_x', 'kurtosis_x', 'skewness_y', 'kurtosis_y']
    for key in required_keys:
        assert key in stats
        assert isinstance(stats[key], (int, float))
```

### 7.4 Test Locally
```bash
# Run quality checks
make quality

# Should pass all tests
```

### 7.5 Push Feature Branch
```bash
git add .
git commit -m "feat: add advanced statistics function"
git push origin feature/new-analysis-function
```

### 7.6 Create Pull Request
- Go to GitHub → Pull requests → New pull request
- Select your feature branch
- Create pull request
- Watch CI pipeline run on the PR

## 🔀 Step 8: Merge and Deploy

### 8.1 Code Review Process
- Review the changes in the PR
- Ensure all CI checks pass
- Approve and merge the PR

### 8.2 Watch Full CI/CD Pipeline
After merge to main:
1. CI tests run again
2. Production image is built
3. Image is pushed to registry
4. Deploy step executes

### 8.3 Verify Deployment
```bash
# Pull the latest image
docker pull YOUR_USERNAME/scipy-app:latest

# Run the updated application
docker run --rm YOUR_USERNAME/scipy-app:latest python main.py
```

## 🚨 Step 9: Handling Failures

### 9.1 Simulate Test Failure
Create a failing test:
```python
def test_failing_example(self):
    """This test will fail"""
    assert 1 == 2  # This will fail
```

### 9.2 Push and Observe
```bash
git add .
git commit -m "test: add failing test"
git push origin main
```

### 9.3 Check Pipeline Failure
- Go to GitHub Actions
- See the failed pipeline
- Review the error logs
- Fix the issue and push again

### 9.4 Fix the Issue
Remove the failing test:
```bash
git revert HEAD
git push origin main
```

## 🔧 Step 10: Advanced Configuration

### 10.1 Add Environment-Specific Configs
Create `config/` directory with environment configs:
```bash
mkdir config
echo "DEBUG=true" > config/development.env
echo "DEBUG=false" > config/production.env
```

### 10.2 Add Health Check Endpoint
If using Flask/FastAPI, add health check:
```python
@app.route('/health')
def health_check():
    return {'status': 'healthy', 'timestamp': time.time()}
```

### 10.3 Configure Monitoring
Add monitoring configuration to docker-compose:
```yaml
  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
```

## 📊 Step 11: Monitoring and Metrics

### 11.1 View Pipeline Analytics
- Go to GitHub → Insights → Actions
- Monitor pipeline success rates
- Track build times and trends

### 11.2 Set Up Notifications
Configure Slack/email notifications in GitHub Actions:
```yaml
- name: Notify on failure
  if: failure()
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### 11.3 Performance Monitoring
Add performance tests to your test suite:
```python
import time

def test_performance_benchmark(self):
    """Test performance of statistical analysis"""
    self.processor.load_data()
    
    start_time = time.time()
    self.processor.statistical_analysis()
    end_time = time.time()
    
    # Should complete within 1 second
    assert end_time - start_time < 1.0
```

## 🎯 Step 12: Production Deployment

### 12.1 Deploy to Cloud Platform
Example for AWS ECS:
```bash
# Configure AWS CLI
aws configure

# Create ECS task definition
aws ecs register-task-definition --cli-input-json file://task-definition.json

# Update service
aws ecs update-service --cluster my-cluster --service scipy-app --task-definition scipy-app
```

### 12.2 Set Up Load Balancer
Configure application load balancer for production traffic.

### 12.3 Set Up Auto-scaling
Configure container auto-scaling based on CPU/memory usage.

## ✅ Verification Checklist

- [ ] Project structure created
- [ ] Docker builds successfully
- [ ] Tests pass locally
- [ ] CI/CD pipeline runs on GitHub
- [ ] Feature branch workflow works
- [ ] Pull request triggers CI
- [ ] Main branch triggers full CD
- [ ] Docker image pushed to registry
- [ ] Error handling works correctly
- [ ] Monitoring configured

## 🎉 Congratulations!

You now have a fully functional SciPy CI/CD pipeline with:
- ✅ Automated testing
- ✅ Code quality checks
- ✅ Docker containerization
- ✅ GitHub Actions CI/CD
- ✅ Registry integration
- ✅ Branch-based deployment
- ✅ Error handling and recovery

## 📚 Next Steps

1. **Scale Up**: Add more test coverage and quality gates
2. **Security**: Add security scanning and vulnerability checks
3. **Performance**: Add performance testing and benchmarking
4. **Monitoring**: Implement comprehensive application monitoring
5. **Multi-Environment**: Set up staging and production environments

## 🆘 Troubleshooting

### Common Issues:
1. **Docker build fails**: Check system dependencies in Dockerfile
2. **Tests fail**: Verify Python path and imports
3. **Pipeline fails**: Check GitHub secrets and permissions
4. **Registry push fails**: Verify Docker Hub credentials

### Getting Help:
- Check pipeline logs in GitHub Actions
- Review Docker build output
- Use `make help` for available commands
- Check container logs with `docker logs <container_name>`

# Python

## Generate requirement.txt
```bash
python -m venv venv
source venv/bin/activate
pip install prometheus_client  prometheus_flask_exporter flask requests
pip freeze > requirements.txt
deactivate
```
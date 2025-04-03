.toml
```
[tool.poetry]  
name = "typeannotations"  
version = "1.0.0"  
description = "Your project description"  
authors = ["Your Name <you@example.com>"]  
readme = "README.md"  
  
packages = [  
    { include = "typeannotations.basic" },  
    { include = "typeannotations.intermediate" }  
]  
  
[tool.poetry.dependencies]  
python = ">=3.12"  
  
[tool.poetry.group.dev.dependencies]  
mypy = "^1.15.0"  
  
[build-system]  
requires = ["poetry-core>=2.0.0,<3.0.0"]  
build-backend = "poetry.core.masonry.api"
```

```
poetry add --dev mypy 
```

```
poetry run mypy .  
```

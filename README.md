# Here is the write-up for the current Quantifying Contributions of Open Source Projects to the Ethereum Universe
Username on Pond: MavMus <br />

email id: kumarankaj110@gmail.com

```python
from intelligent_retriever import create_retriever, RetrievalConfig

config = RetrievalConfig(
    db_host="localhost",
    db_name="vector_db",
    db_user="postgres",
    db_password="postgres123",
    redis_url="redis://localhost:6379"
)

retriever = create_retriever(config=config)

# Retrieve with user context
result = retriever.retrieve("What is machine learning?", user_id="user123")

print(f"Strategy: {result['strategy_used']}")
print(f"Context Used: {result['context_used']}")
print(f"Documents: {result['documents']}")
```

```python

print(f"working")
```

# Example of using Tracing module

test message:
	
	curl -H "X-Correlation-ID: test-1234" -X "POST" --json '{"transactionId": "trx-5001", "orderId": "ord-0001"}'  http://localhost:8081/trace
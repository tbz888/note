### Sample
```json
{
  "timestamp": "1548000267000",
  "type": "select",
  "table": "test",
  "conditions": [
    {
      "field": "csn",
      "operator": "=",
      "values": [
        "123",
        "456",
        "789"
      ]
    },
    {
      "field": "cleared",
      "operator": "=",
      "values": [
        "1"
      ]
    },
    {
      "field": "occurTime",
      "operator": "between",
      "values": [
        "1548000067000",
        "1548000167000"
      ]
    }
  ]
}
```

### TreeModel
> String -> JsonNode

> JsonNode -> String

### DataBind
> String -> JavaBean

> JavaBean -> String

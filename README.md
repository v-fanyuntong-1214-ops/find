```typescript
const [sortOrder, setSortOrder] = useState<"new" | "old">("new");

const sortedAnswers = [...answers].sort((a, b) => {
  const dateA = new Date(a.createdAt).getTime();
  const dateB = new Date(b.createdAt).getTime();

  return sortOrder === "new" ? dateB - dateA : dateA - dateB;
});

```
```typescript
return (
  <div className="table-responsive">
    <div className="mb-2 text-end">
      <select
        className="form-select w-auto d-inline-block"
        value={sortOrder}
        onChange={(e) => setSortOrder(e.target.value as "new" | "old")}
      >
        <option value="new">新しい順</option>
        <option value="old">古い順</option>
      </select>
    </div>

    <table className="table table-striped table-bordered align-middle mb-0">
```


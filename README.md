AnswerRow
```typescript
function AnswerRow(props: { answer: Answer; userId: number }) {
```

```typescript
function handleDeleteClick(answerId: number) {
  fetch(`http://localhost:8080/api/answer/${answerId}`, {
    method: "DELETE",
  }).then((response: Response) => {
    if (response.ok) {
      window.location.reload();
    }
  });
}
```


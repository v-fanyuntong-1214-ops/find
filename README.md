```typescript
  const handleEditSave = () => {
    const requestBody = {
      mainText: editText,
    };

    api
      .sendJson(`/api/answer/${props.answer.answerId}`, requestBody, {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
        },
      })
      .then((response: Response) => {
        if (response.ok) {
          window.location.reload();
        }
      });
  };

```

```typescript


<td>
  {isEditing ? (
    <textarea
      value={editText}
      onChange={(e) => setEditText(e.target.value)}
    />
  ) : (
    props.answer.mainText
  )}
</td>

```


```typescript

<td>
  {isEditing ? (
    <>
      <button type="button" onClick={handleEditSave}>
        保存
      </button>
      <button type="button" onClick={() => setIsEditing(false)}>
        キャンセル
      </button>
    </>
  ) : (
    <button type="button" onClick={() => setIsEditing(true)}>
      編集
    </button>
  )}

  <button
    type="button"
    onClick={() =>
      handleDeleteClick(
        props.titleId,
        props.userId,
        props.answer.answerId,
        api,
        navigate,
      )
    }
  >
    削除
  </button>
</td>

```






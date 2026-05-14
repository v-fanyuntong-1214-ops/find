```java
package com.example.simplex.solare131.server.controller.dto;

import jakarta.validation.constraints.NotBlank;

public class AnswerUpdateDto {

    @NotBlank
    private String mainText;

    public String getMainText() {
        return mainText;
    }

    public void setMainText(String mainText) {
        this.mainText = mainText;
    }
}
```

```java
@PutMapping("/{answerId}")
@ResponseStatus(HttpStatus.NO_CONTENT)
public void update(
        @PathVariable int answerId,
        @RequestBody @Valid AnswerUpdateDto input
) {
    answerService.update(answerId, input.getMainText());
}
```
PUT /api/answer/{titleId}/{answerId}
```java
public void update(int answerId, String mainText) {
    answerRepository.updateMainText(answerId, mainText);
}
```

```java
public void updateMainText(int answerId, String mainText) {
    jdbcTemplate.update("""
        UPDATE answer
        SET main_text = ?
        WHERE answer_id = ?
        """,
        mainText,
        answerId
    );
}
public void updateMainText(int answerId, String mainText) {
    jdbcTemplate.update("""
        UPDATE answer
        SET main_text = ?
        WHERE answer_id = ?
        """,
        mainText,
        answerId
    );
}
```

```typescript
function handleEditClick(answerId: number, currentMainText: string) {
  const newMainText = window.prompt("回答内容を編集してください", currentMainText);

  if (newMainText === null) {
    return;
  }

  if (newMainText.trim() === "") {
    alert("回答内容を入力してください");
    return;
  }

  fetch(`http://localhost:8080/api/answer/${props.titleId}/${answerId}`, {
    method: "PUT",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      mainText: newMainText,
    }),
  }).then((response: Response) => {
    if (response.ok) {
      window.location.reload();
    } else {
      alert("編集に失敗しました");
    }
  });
}
```
```typescript
<button
  type="button"
  onClick={() => handleEditClick(props.answer.answerId, props.answer.mainText)}
>
  編集
</button>

```



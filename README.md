```typescript
import { useEffect, useState } from "react";
import { useParams } from "react-router";

type Answer = {
  answerId: number;
  userName: string;
  mainText: string;
  createdAt: string;
  goodCount: number;
};

function handleGoodClick(userId: number, answerId: number) {
  fetch(`${import.meta.env.VITE_REST_BASE_URL}/api/answer/good`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      userId: userId,
      answerId: answerId,
    }),
  }).then((response: Response) => {
    if (response.ok) {
      window.location.reload();
    }
  });
}

function AnswerRow(props: { answer: Answer; userId: number; titleId: string }) {
  function handleDeleteClick(answerId: number) {
    fetch(
      `${import.meta.env.VITE_REST_BASE_URL}/api/answer/${props.titleId}/${answerId}`,
      {
        method: "DELETE",
      }
    ).then((response: Response) => {
      if (response.ok) {
        window.location.reload();
      }
    });
  }

  return (
    <tr>
      <td>{props.answer.userName}</td>
      <td>{props.answer.mainText}</td>
      <td>
        {new Date(props.answer.createdAt).toLocaleDateString("ja-JP", {
          year: "numeric",
          month: "2-digit",
          day: "2-digit",
          hour: "2-digit",
          minute: "2-digit",
        })}
      </td>
      <td className="text-end">
        <button
          className="like"
          type="button"
          onClick={() => handleGoodClick(props.userId, props.answer.answerId)}
        >
          ♡
        </button>

        <span>{props.answer.goodCount}</span>

        <button
          type="button"
          onClick={() => handleDeleteClick(props.answer.answerId)}
        >
          削除
        </button>
      </td>
    </tr>
  );
}

export default function AnswerList() {
  const [answers, setAnswers] = useState<Answer[]>([]);
  const { title_id } = useParams();

  const user_id = 1;

  useEffect(() => {
    fetch(`${import.meta.env.VITE_REST_BASE_URL}/api/answer/${title_id}`)
      .then((response: Response) => response.json())
      .then((data: Answer[]) => setAnswers(data));
  }, [title_id]);

  return (
    <div className="table-responsive">
      <table className="table table-striped table-bordered align-middle mb-0">
        <thead className="table-light">
          <tr>
            <th scope="col">回答者名</th>
            <th scope="col">回答内容</th>
            <th scope="col">回答日時</th>
            <th scope="col">いいねボタン</th>
          </tr>
        </thead>

        <tbody>
          {answers.length > 0 ? (
            answers.map((answer: Answer) => (
              <AnswerRow
                key={answer.answerId}
                answer={answer}
                userId={user_id}
                titleId={title_id ?? ""}
              />
            ))
          ) : (
            <tr>
              <td colSpan={4}>No data</td>
            </tr>
          )}
        </tbody>
      </table>
    </div>
  );
}
```

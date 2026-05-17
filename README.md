```typescript
import {useEffect, useState} from "react";
import type {Answer} from "../../models/Answer.ts";
import {type NavigateFunction, useLocation, useNavigate, useParams} from "react-router";
import "./AnswerList.css";
import {useApi} from "../../lib/useApi.ts";
import type {UseApiFetchOptions} from "../../lib/UseApiFetchOption.ts";

function handleGoodClick(
    titleId: string,
    userId: number,
    answerId: number,
    api: {
        fetch: (path: `/${string}`, options?: UseApiFetchOptions) => Promise<Response>;
        sendJson: <TBody>(
            path: `/${string}`,
            body: TBody,
            options?: UseApiFetchOptions
        ) => Promise<Response>;
    },
    navigate: NavigateFunction
) {
    const requestBody = {
        userId: userId,
        answerId: answerId,
    };

    api.sendJson("/api/answer/good", requestBody, {
        method: "POST",
        headers: {
            "Content-Type": "application/json",
        },
    }).then((response: Response) => {
        if (response.ok) {
            navigate(`/title/${titleId}/${userId}`);
        }
    });
}

function handleDeleteClick(
    titleId: string,
    userId: number,
    answerId: number,
    api: {
        fetch: (path: `/${string}`, options?: UseApiFetchOptions) => Promise<Response>;
        sendJson: <TBody>(
            path: `/${string}`,
            body: TBody,
            options?: UseApiFetchOptions
        ) => Promise<Response>;
    },
    navigate: NavigateFunction
) {
    api.fetch(`/api/answer/${answerId}`, {
        method: "DELETE",
    }).then((response: Response) => {
        if (response.ok) {
            navigate(`/title/${titleId}/${userId}`);
        }
    });
}

function AnswerRow(props: { answer: Answer; userId: number; titleId: string }) {
    const api = useApi();
    const navigate = useNavigate();

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
                    onClick={() =>
                        handleGoodClick(
                            props.titleId,
                            props.userId,
                            props.answer.answerId,
                            api,
                            navigate
                        )
                    }
                >
                    ♡
                </button>
                <span>{props.answer.goodCount}</span>
                <button
                    type="button"
                    onClick={() =>
                        handleDeleteClick(
                            props.titleId,
                            props.userId,
                            props.answer.answerId,
                            api,
                            navigate
                        )
                    }
                >
                    削除
                </button>
            </td>
        </tr>
    );
}

export default function AnswerList() {
    const [answers, setAnswers] = useState<Answer[]>([]);
    const {title_id = ""} = useParams();

    const user_id = 1;

    const [sortOrder, setSortOrder] = useState<"new" | "old">("new");

    // 投稿後の再アクセス時にデータを読み込んでくるように作成
    const location = useLocation();

    useEffect(() => {
        fetch(`http://localhost:8080/api/answer/${title_id}`)
            .then((response: Response) => response.json())
            .then((data: Answer[]) => setAnswers(data));
    }, [title_id, location]);

    return (
        <div className="table-responsive">
            <select
                value={sortOrder}
                onChange={(e) => setSortOrder(e.target.value as "new" | "old")}
            >
                <option value="new">新しい順</option>
                <option value="old">古い順</option>
            </select>

            <table className="table table-striped table-bordered align-middle mb-0">
                <thead className="table-light">
                    <tr>
                        <th scope="col">回答者名</th>
                        <th scope="col">回答内容</th>
                        <th scope="col">回答日時</th>
                        <th scope="col">いいね、編集、削除</th>
                    </tr>
                </thead>

                <tbody>
                    {[...answers]
                        .sort((a: Answer, b: Answer) => {
                            if (sortOrder === "new") {
                                return (
                                    new Date(b.createdAt).getTime() -
                                    new Date(a.createdAt).getTime()
                                );
                            } else {
                                return (
                                    new Date(a.createdAt).getTime() -
                                    new Date(b.createdAt).getTime()
                                );
                            }
                        })
                        .map((answer: Answer) => (
                            <AnswerRow
                                key={answer.answerId}
                                answer={answer}
                                userId={user_id}
                                titleId={title_id}
                            />
                        ))}
                </tbody>
            </table>
        </div>
    );
}
```

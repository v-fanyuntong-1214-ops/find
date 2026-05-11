```java
public record AnswerInputRequest(
        Integer titleId,
        Integer userId,
        String mainText
) {
}
```

```java
@RestController
@RequestMapping("/api/answer-input")
public class AnswerInputController {

    private final AnswerInputService answerInputService;

    public AnswerInputController(
            AnswerInputService answerInputService
    ) {
        this.answerInputService = answerInputService;
    }

    @PostMapping("")
    public void post(
            @RequestBody AnswerInputRequest request
    ) {
        answerInputService.post(request);
    }
}
```

```java
@Service
public class AnswerInputService {

    private final AnswerInputRepository repository;

    public AnswerInputService(
            AnswerInputRepository repository
    ) {
        this.repository = repository;
    }

    public void post(AnswerInputRequest request) {

        if (request.mainText() == null ||
            request.mainText().isBlank()) {

            throw new IllegalArgumentException(
                    "回答を入力してください"
            );
        }

        repository.insert(request);
    }
}
```

```java
@Repository
public class AnswerInputRepository {

    private final JdbcTemplate jdbcTemplate;

    public AnswerInputRepository(
            JdbcTemplate jdbcTemplate
    ) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public void insert(
            AnswerInputRequest request
    ) {

        jdbcTemplate.update("""
            INSERT INTO answer(
                title_id,
                user_id,
                main_text
            )
            VALUES (?, ?, ?)
            """,
            request.titleId(),
            request.userId(),
            request.mainText()
        );
    }
}
```

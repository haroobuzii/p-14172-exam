# p-14172-exam

스프링부트로 만든 **게시글/댓글 CRUD REST API** 과제입니다.
기능마다 **테스트를 먼저 작성(RED)하고, 통과하도록 구현(GREEN)하는 TDD 방식**으로 진행했습니다.

참고: [몰입코딩 - 스프링부트, REST API](https://www.slog.gg/p/14172) (1강 ~ 56강, 2부 REST API by TDD)

## 과제 항목

- [x] 프로젝트를 제로에서 시작
- [x] 샘플 데이터 글 3개, 댓글 5개
- [x] 글 CRUD 엔드포인트 구현 by TDD (작성, 수정, 삭제, 다건조회, 단건조회)
- [x] 댓글 CRUD 엔드포인트 구현 by TDD (다건조회, 단건조회, 삭제, 수정, 작성)
- [x] 커밋 메시지를 자세히 작성
- [x] 각 커밋마다 `001`, `002` … 태그

## 기술 스택

| 구분 | 사용 기술 |
|---|---|
| 언어 | Java 25 |
| 프레임워크 | Spring Boot 4.1.2 (Web MVC, Data JPA, Validation) |
| DB | H2 (개발: 파일 DB, 테스트: 인메모리 DB) |
| 기타 | Lombok, Gradle |
| 테스트 | JUnit 5, MockMvc |

## 실행 방법

```bash
./gradlew bootRun
```

- 서버 주소: `http://localhost:8080`
- 처음 실행하면 샘플 데이터가 자동 생성됩니다.
  - 글 1: 댓글 3개 / 글 2: 댓글 2개 / 글 3: 댓글 없음

## 테스트 실행

```bash
./gradlew test
```

- `test` 프로필(인메모리 DB)로 실행되어 개발용 DB에는 영향을 주지 않습니다.
- 각 테스트는 `@Transactional`로 끝나면 롤백되어 서로 영향을 주지 않습니다.

## API

### 글

| 기능 | 메서드 | 주소 | 성공 응답 |
|---|---|---|---|
| 다건조회 | `GET` | `/api/v1/posts` | `200` 글 배열 |
| 단건조회 | `GET` | `/api/v1/posts/{id}` | `200` 글 하나 |
| 작성 | `POST` | `/api/v1/posts` | `201` RsData + 작성된 글 |
| 수정 | `PUT` | `/api/v1/posts/{id}` | `200` RsData |
| 삭제 | `DELETE` | `/api/v1/posts/{id}` | `200` RsData |

### 댓글

| 기능 | 메서드 | 주소 | 성공 응답 |
|---|---|---|---|
| 다건조회 | `GET` | `/api/v1/posts/{postId}/comments` | `200` 댓글 배열 |
| 단건조회 | `GET` | `/api/v1/posts/{postId}/comments/{id}` | `200` 댓글 하나 |
| 작성 | `POST` | `/api/v1/posts/{postId}/comments` | `201` RsData + 작성된 댓글 |
| 수정 | `PUT` | `/api/v1/posts/{postId}/comments/{id}` | `200` RsData |
| 삭제 | `DELETE` | `/api/v1/posts/{postId}/comments/{id}` | `200` RsData |

### 요청 본문 (JSON)

글 작성/수정

```json
{
  "title": "제목",
  "content": "내용"
}
```

댓글 작성/수정

```json
{
  "content": "내용"
}
```

입력값 규칙

- 글 제목: 2 ~ 100자, 글 내용: 2 ~ 5000자
- 댓글 내용: 2 ~ 100자

### 응답 형식

조회 성공은 데이터를 그대로 응답합니다.

```json
{
  "id": 1,
  "createDate": "2026-11-18T14:00:00.000000",
  "modifyDate": "2026-11-18T14:00:00.000000",
  "title": "제목 1",
  "content": "내용 1"
}
```

작성/수정/삭제와 실패 응답은 `RsData`(`resultCode`, `msg`, `data`) 형식으로 응답합니다.
`resultCode`의 앞자리(`201-1`의 `201`)가 HTTP 응답코드로 사용됩니다. (`ResponseAspect`)

```json
{
  "resultCode": "201-1",
  "msg": "4번 글이 작성되었습니다.",
  "data": {
    "id": 4,
    "createDate": "2026-11-18T14:00:00.000000",
    "modifyDate": "2026-11-18T14:00:00.000000",
    "title": "제목",
    "content": "내용"
  }
}
```

### 실패 응답

| 상황 | HTTP | resultCode | msg |
|---|---|---|---|
| 없는 글/댓글 | `404` | `404-1` | `해당 데이터가 존재하지 않습니다.` |
| 입력값 검증 실패 | `400` | `400-1` | `필드-규칙-메시지` 형태, 여러 개면 줄바꿈 (예: `title-NotBlank-must not be blank`) |
| JSON 형식 오류 | `400` | `400-1` | `요청 본문이 올바르지 않습니다.` |

## 커밋(태그) 내역

`[RED]`는 실패하는 테스트를 먼저 작성한 커밋, `[GREEN]`은 그 테스트를 통과시킨 구현 커밋입니다.
태그로 각 시점의 코드를 바로 확인할 수 있습니다. 예: `git checkout 004`

| 태그 | 커밋 |
|---|---|
| `001` | 프로젝트 생성 |
| `002` | 샘플 데이터 글 3개, 댓글 5개 자동 생성 |
| `003` | [RED] 글 작성 테스트 작성 |
| `004` | [GREEN] 글 작성 구현 (RsData, ResponseAspect, PostDto) |
| `005` | [RED] 글 수정 테스트 작성 |
| `006` | [GREEN] 글 수정 구현 |
| `007` | [RED] 글 삭제 테스트 작성 |
| `008` | [GREEN] 글 삭제 구현 |
| `009` | [RED] 글 단건조회 테스트 작성 |
| `010` | [GREEN] 글 단건조회 구현 |
| `011` | [RED] 글 다건조회 테스트 작성 |
| `012` | [GREEN] 글 다건조회 구현 |
| `013` | [RED] 글 단건조회 404 테스트 작성 |
| `014` | [GREEN] 없는 데이터 요청 시 404 응답 (GlobalExceptionHandler) |
| `015` | [RED] 글 작성 입력값 검증 400 테스트 작성 |
| `016` | [GREEN] 글 작성/수정 입력값 검증 (@Valid, 400 응답) |
| `017` | [RED] 잘못된 JSON 양식 400 테스트 작성 |
| `018` | [GREEN] 잘못된 JSON 양식 400 응답 |
| `019` | [RED] 댓글 다건조회 테스트 작성 |
| `020` | [GREEN] 댓글 다건조회 구현 (PostCommentDto) |
| `021` | [RED] 댓글 단건조회 테스트 작성 |
| `022` | [GREEN] 댓글 단건조회 구현 |
| `023` | [RED] 댓글 삭제 테스트 작성 |
| `024` | [GREEN] 댓글 삭제 구현 (orphanRemoval) |
| `025` | [RED] 댓글 수정 테스트 작성 |
| `026` | [GREEN] 댓글 수정 구현 |
| `027` | [RED] 댓글 작성 테스트 작성 |
| `028` | [GREEN] 댓글 작성 구현 (flush) |
| `029` | 댓글 404 테스트 추가 (기존 예외 처리로 바로 통과) |
| `030` | README 작성 |

## 테스트 목록 (총 20개)

| 테스트 클래스 | 테스트 |
|---|---|
| `BackApplicationTests` | 앱이 정상적으로 켜지는지 |
| `BaseInitDataTest` | 샘플 데이터가 글 3개, 댓글 5개인지 |
| `ApiV1PostControllerTest` (9개) | 작성 / 수정 / 삭제 / 단건조회 / 다건조회 / 없는 글 단건조회(404) / 제목 없이 작성(400) / 내용 없이 작성(400) / 잘못된 JSON(400) |
| `ApiV1PostCommentControllerTest` (9개) | 다건조회 / 단건조회 / 삭제 / 수정 / 작성 / 내용 없이 작성(400) / 없는 글의 댓글 다건조회(404) / 없는 댓글 단건조회(404) / 다른 글의 댓글 번호로 단건조회(404) |

## 프로젝트 구조

```
src/main/java/com/back
├── BackApplication.java
├── domain/post
│   ├── post
│   │   ├── controller/ApiV1PostController.java         # 글 API
│   │   ├── dto/PostDto.java                            # 글 응답용 데이터
│   │   ├── entity/Post.java                            # 글 엔티티
│   │   ├── repository/PostRepository.java
│   │   └── service/PostService.java
│   └── postComment
│       ├── controller/ApiV1PostCommentController.java  # 댓글 API
│       ├── dto/PostCommentDto.java                     # 댓글 응답용 데이터
│       └── entity/PostComment.java                     # 댓글 엔티티
└── global
    ├── aspect/ResponseAspect.java                      # resultCode → HTTP 응답코드
    ├── globalExceptionHandler/GlobalExceptionHandler.java  # 400, 404 처리
    ├── initData/BaseInitData.java                      # 샘플 데이터 생성
    ├── jpa/entity/BaseEntity.java                      # id, 작성일, 수정일 공통
    └── rsData/RsData.java                              # 공통 응답 형식
```

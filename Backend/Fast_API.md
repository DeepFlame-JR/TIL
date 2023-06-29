Fast_API

# Fast API
- Python 기반의 웹 프레임워크로, 빠른 속도와 간결한 문법을 특징
- 특징
    - 빠른 속도
        - ASGI(Asynchronous Server Gateway Interface)를 기반으로 동작하여 비동기 처리를 지원
        - 높은 처리량과 낮은 지연 시간을 제공
        - 기존 Python 웹서버 프레임 워크인 Django, Flask보다 빠름
    - 간결한 문법
        - FAST API는 Python의 타입 힌팅(Type Hinting)을 기반으로 한 선언적인 문법을 사용
        - 개발자는 코드를 더 명확하게 작성할 수 있고, 자동 문서화와 유효성 검사 등의 기능을 제공
    - 자동 문서화
        - FAST API는 OpenAPI(Swagger) 스펙을 자동으로 생성하여 API의 문서화
        - 개발자는 주석을 통해 API의 설명과 파라미터 정보, 응답 형식 등을 작성하면, 이를 기반으로 자동으로 API 문서가 생성
    - 보안 기능
        - OAuth2와 JWT(JSON Web Tokens) 등과 같은 보안 기능을 내장
    - 데이터 유효성 검사
        - FAST API는 Python의 타입 힌팅을 활용하여 입력 데이터의 유효성을 검사
        - 입력 데이터의 유효성 검증을 간단하게 처리
    - 자동화된 테스트
        - FAST API는 자동화된 테스트를 지원하여 개발자가 작성한 API 엔드포인트의 테스트를 쉽게 수행
- 예제
```py
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# 임시로 데이터를 저장할 리스트
users = []

# 사용자 모델 정의
class User(BaseModel):
    id: int
    name: str
    age: int

# 사용자 생성
@app.post("/users")
def create_user(user: User):
    users.append(user)
    return user

# 모든 사용자 조회
@app.get("/users")
def get_all_users():
    return users

# 특정 사용자 조회
@app.get("/users/{user_id}")
def get_user(user_id: int):
    for user in users:
        if user.id == user_id:
            return user
    return {"message": "User not found"}

# 사용자 정보 업데이트
@app.put("/users/{user_id}")
def update_user(user_id: int, updated_user: User):
    for i, user in enumerate(users):
        if user.id == user_id:
            users[i] = updated_user
            return updated_user
    return {"message": "User not found"}

# 사용자 삭제
@app.delete("/users/{user_id}")
def delete_user(user_id: int):
    for i, user in enumerate(users):
        if user.id == user_id:
            del users[i]
            return {"message": "User deleted"}
    return {"message": "User not found"}
```
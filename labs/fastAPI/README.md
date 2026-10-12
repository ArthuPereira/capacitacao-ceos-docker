# FastAPI CRUD - Ceos Jr.

API REST desenvolvida em FastAPI com SQLAlchemy e PostgreSQL assíncrono (`asyncpg`), utilizada no **Laboratório 2** da Capacitação Docker.

## Endpoints

A aplicação disponibiliza operações de CRUD para gerenciamento de usuários:

- `POST /users/` - Cadastra um novo usuário (`name`, `email`)
- `GET /users/` - Lista todos os usuários cadastrados
- `GET /users/{id}` - Obtém os detalhes de um usuário por ID
- `PUT /users/{id}` - Atualiza dados de um usuário por ID
- `DELETE /users/{id}` - Remove um usuário por ID

A documentação interativa (Swagger UI) fica acessível em:
`http://localhost:8000/docs`

## Execução com Docker

Consulte o passo a passo completo no guia do laboratório:
[lab-02.md](./lab-02.md)

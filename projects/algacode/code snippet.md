```go
  

func (r *repository) ListSessions(limit, offset int) ([]*model.Session, error) {
query := `
SELECT uuid, status, start_at, finish_at, created_at
FROM coding_sessions
ORDER BY created_at DESC
LIMIT $1 OFFSET $2

`
rows, err := r.db.Query(query, limit, offset)
if err != nil {
return nil, err
}
defer rows.Close()

var sessions []*model.Session
for rows.Next() {
session := &model.Session{}
err := rows.Scan(
&session.UUID,
&session.Status,
&session.StartAt,

&session.FinishAt,

&session.CreatedAt,

)

if err != nil {

return nil, err

}

sessions = append(sessions, session)

}

return sessions, nil

}

  

// Message operations

func (r *repository) CreateMessage(message *model.Message) error {

query := `

INSERT INTO messages (sessions_uuid, sender, message, created_at)

VALUES ($1, $2, $3, $4)

RETURNING id

`

return r.db.QueryRow(query, message.SessionUUID, message.Sender, message.Message, message.CreatedAt).Scan(&message.ID)

}

  

func (r *repository) GetMessagesBySessionUUID(sessionUUID string) ([]*model.Message, error) {

query := `

SELECT id, sessions_uuid, sender, message, created_at

FROM messages

WHERE sessions_uuid = $1

ORDER BY created_at ASC

`

rows, err := r.db.Query(query, sessionUUID)

if err != nil {

return nil, err

}

defer rows.Close()

  

var messages []*model.Message

for rows.Next() {

message := &model.Message{}

err := rows.Scan(

&message.ID,

&message.SessionUUID,

&message.Sender,

&message.Message,

&message.CreatedAt,

)

if err != nil {

return nil, err

}

messages = append(messages, message)

}

return messages, nil

}

  

func (r *repository) GetLastMessageBySessionUUID(sessionUUID string) (*model.Message, error) {

query := `

SELECT id, sessions_uuid, sender, message, created_at

FROM messages

WHERE sessions_uuid = $1

ORDER BY created_at DESC

LIMIT 1

`

message := &model.Message{}

err := r.db.QueryRow(query, sessionUUID).Scan(

&message.ID,

&message.SessionUUID,

&message.Sender,

&message.Message,

&message.CreatedAt,

)

if err != nil {

return nil, err

}

return message, nil

}

  

// Summary operations

func (r *repository) CreateSummary(summary *model.Summary) error {

query := `

INSERT INTO summaries (sessions_uuid, summary, created_at)

VALUES ($1, $2, $3)

RETURNING id

`

return r.db.QueryRow(query, summary.SessionUUID, summary.Summary, summary.CreatedAt).Scan(&summary.ID)

}

  

func (r *repository) GetSummaryBySessionUUID(sessionUUID string) (*model.Summary, error) {

query := `

SELECT id, sessions_uuid, summary, created_at

FROM summaries

WHERE sessions_uuid = $1

`

summary := &model.Summary{}

err := r.db.QueryRow(query, sessionUUID).Scan(

&summary.ID,

&summary.SessionUUID,

&summary.Summary,

&summary.CreatedAt,

)

if err != nil {

return nil, err

}

return summary, nil

}

  

func (r *repository) UpdateSummary(sessionUUID string, summary string) error {

query := `

UPDATE summaries

SET summary = $1

WHERE sessions_uuid = $2

`

_, err := r.db.Exec(query, summary, sessionUUID)

return err

}

  

// Task operations

func (r *repository) CreateTask(task *model.Task) error {

query := `

INSERT INTO tasks (body, solution, created_at)

VALUES ($1, $2, $3)

RETURNING id

`

return r.db.QueryRow(query, task.Body, task.Solution, task.CreatedAt).Scan(&task.ID)

}

  

func (r *repository) GetTaskByID(id int64) (*model.Task, error) {

query := `

SELECT id, body, solution, created_at

FROM tasks

WHERE id = $1

`

task := &model.Task{}

err := r.db.QueryRow(query, id).Scan(

&task.ID,

&task.Body,

&task.Solution,

&task.CreatedAt,

)

if err != nil {

return nil, err

}

return task, nil

}

  

func (r *repository) UpdateTaskSolution(id int64, solution string) error {

query := `

UPDATE tasks

SET solution = $1

WHERE id = $2

`

_, err := r.db.Exec(query, solution, id)

return err

}

  

func (r *repository) ListTasks(limit, offset int) ([]*model.Task, error) {

query := `

SELECT id, body, solution, created_at

FROM tasks

ORDER BY created_at DESC

LIMIT $1 OFFSET $2

`

rows, err := r.db.Query(query, limit, offset)

if err != nil {

return nil, err

}

defer rows.Close()

  

var tasks []*model.Task

for rows.Next() {

task := &model.Task{}

err := rows.Scan(

&task.ID,

&task.Body,

&task.Solution,

&task.CreatedAt,

)

if err != nil {

return nil, err

}

tasks = append(tasks, task)

}

return tasks, nil

}

  

// AI Request operations

func (r *repository) SendRequest(req *model.Request) (*model.Response, error) {

jsonData, err := json.Marshal(req)

if err != nil {

return nil, err

}

  

httpReq, err := http.NewRequest("POST", "https://openrouter.ai/api/v1/chat/completions", bytes.NewBuffer(jsonData))

if err != nil {

return nil, err

}

  

httpReq.Header.Set("Authorization", "Bearer "+r.apiKey)

httpReq.Header.Set("Content-Type", "application/json")

  

resp, err := r.client.Do(httpReq)

if err != nil {

return nil, err

}

defer resp.Body.Close()

  

body, err := io.ReadAll(resp.Body)

if err != nil {

return nil, err

}

  

var response model.Response

if err := json.Unmarshal(body, &response); err != nil {

return nil, err

}

  

return &response, nil

}
```
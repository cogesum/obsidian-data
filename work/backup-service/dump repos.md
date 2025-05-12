
Формат данных в gitea:
![[Pasted image 20250509134112.png]]

```bash
# command to make dump repo
gitea dump-repo --clone_addr http://gitea:3000/user1/test_dump_repo1 --repo_dir /data/repos --git_service gitea
```

#### реализация дампа одного репозитория

Точка старта с эндпоинт POST `/dump/:id` .
Храним данные из бд в yaml. И их перегоняем из БД в БД
RepositoryDumper предоставляет интерфейс Uploader, где получает данные от downloader'a и через маршал перегоняет их в yaml и записывает на диск.


``` go
type RepositoryDumper struct {  
    ctx             context.Context  
    baseDir         string  
    repoOwner       string  
    repoName        string  
    opts            base.MigrateOptions  
    milestoneFile   *os.File  
    labelFile       *os.File  
    releaseFile     *os.File  
    issueFile       *os.File  
    commentFiles    map[int64]*os.File  
    pullrequestFile *os.File  
    reviewFiles     map[int64]*os.File  
  
    gitRepo     *git.Repository  
    prHeadCache map[string]string  
}
```

реализовать retry: modules/migration/retry_downloader.go:11

для оптимизации памяти, делаем стрим даты в файл:
```go
import (
    "bufio"
    "fmt"
    "os"
    "path/filepath"

    "gopkg.in/yaml.v3"
)

// StreamMilestones writes milestones to a YAML file incrementally
func (g *RepositoryDumper) StreamMilestones(milestoneChan <-chan *Milestone, batchSize int) error {
    filePath := filepath.Join(g.baseDir, "milestone.yml")
    file, err := os.Create(filePath)
    if err != nil {
        return fmt.Errorf("failed to create milestone file: %w", err)
    }
    defer file.Close()

    writer := bufio.NewWriter(file)
    encoder := yaml.NewEncoder(writer)
    defer encoder.Close()

    batch := make([]*Milestone, 0, batchSize)

    for milestone := range milestoneChan {
        batch = append(batch, milestone)

        // Write batch when full
        if len(batch) >= batchSize {
            if err := encoder.Encode(batch); err != nil {
                return fmt.Errorf("failed to encode milestone batch: %w", err)
            }
            batch = make([]*Milestone, 0, batchSize)
            writer.Flush()
        }
    }

    // Write remaining items
    if len(batch) > 0 {
        if err := encoder.Encode(batch); err != nil {
            return fmt.Errorf("failed to encode final milestone batch: %w", err)
        }
        writer.Flush()
    }

    return nil
}
```
#### дамп всех репозиториев

# Chunk reading

Importing a large file can have a huge impact on the memory usage, as the library will try to load the entire sheet into memory.

To mitigate this increase in memory usage, you can use the `WithChunkReading` concern. This will read the spreadsheet in chunks and keep the memory usage under control.

```php
namespace App\Imports;

use App\User;
use Maatwebsite\Excel\Concerns\ToModel;
use Maatwebsite\Excel\Concerns\WithChunkReading;

class UsersImport implements ToModel, WithChunkReading
{
    public function model(array $row)
    {
        return new User([
            'name' => $row[0],
        ]);
    }
    
    public function chunkSize(): int
    {
        return 1000;
    }
}
```

::: warning
A chunk size of `1000` will not be the most optimal situation for your export. Play around with this number to find the sweet spot.
:::

### Using it together with Batch Inserts

The most ideal situation (regarding time and memory consumption) you will find when combining batch inserts and chunk reading.

```php
namespace App\Imports;

use App\User;
use Maatwebsite\Excel\Concerns\ToModel;
use Maatwebsite\Excel\Concerns\WithBatchInserts;
use Maatwebsite\Excel\Concerns\WithChunkReading;

class UsersImport implements ToModel, WithBatchInserts, WithChunkReading
{
    public function model(array $row)
    {
        return new User([
            'name' => $row[0],
        ]);
    }
    
    public function batchSize(): int
    {
        return 1000;
    }
    
    public function chunkSize(): int
    {
        return 1000;
    }
}
```

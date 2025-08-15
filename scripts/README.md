# Scripts

## delete-all-data.js

⚠️ **DANGER: This script permanently deletes ALL data!** ⚠️

### What it does

This script deletes all data from:
- **OpenAuth DynamoDB table**: All authentication sessions, codes, and related data
- **Aurora PostgreSQL database**: All users and tasks

### Safety features

1. **Single confirmation prompt**: The script requires you to type "yes" once to confirm deletion
2. **Clear warnings**: Shows exactly what will be deleted
3. **Graceful cancellation**: Easy to cancel at any point
4. **Resource verification**: Reads actual resource names from SST outputs
5. **Aurora auto-pause resilience**: Automatically retries when Aurora is resuming from auto-pause

### Usage

```bash
# Run the script
npm run delete-all-data

# Or directly
tsx scripts/delete-all-data.ts
```


### Aurora auto-pause handling

Aurora Serverless databases automatically pause when not in use to save costs. When the script tries to access a paused database, you'll see:

```
💤 Aurora database is resuming (attempt 1/3). Retrying in 10 seconds...
```

The script will automatically:
- Detect auto-pause/resume scenarios
- Wait 10 seconds between retry attempts  
- Retry up to 3 times for each operation
- Provide helpful error messages if all retries fail

No action is required from you - just wait for the retries to complete.

### When to use

- **Development reset**: Clean slate for testing
- **Demo preparation**: Reset before showing the app
- **Bug investigation**: Eliminate data-related issues
- **Privacy compliance**: Complete data removal

### Prerequisites

- Valid AWS credentials with access to DynamoDB and RDS
- SST deployment outputs (`.sst/outputs.json` file)
- Node.js environment with required AWS SDK packages
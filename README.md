# MEMES?

That's a dumb simple repo for having some memes for deployments.  
Just to add a bit of fun when everything might get sad.

## How it looks?
<img width="509" height="328" alt="image" src="https://github.com/user-attachments/assets/14b05c24-f0d8-4f39-8444-a8a6fa0d9f58" />


## How to use it?

1. Get the image URL 
```
image=$(curl -s "https://api.github.com/repos/edoardodavini/memes/contents/failure" | jq -r '.[] | select(.type=="file") | .download_url' | shuf -n 1)
```

2. Use the image URL
```
  {
    "type": "Image",
    "url": "'"$image"'"
  }
```

> you can obviously just do 
```
  {
    "type": "Image",
    "url": "'"$(curl -s "https://api.github.com/repos/edoardodavini/memes/contents/failure" | jq -r '.[] | select(.type=="file") | .download_url' | shuf -n 1)"'"
  }
```


### Full example

```yml
  notify-failure:
    runs-on: ubuntu-latest
    env:
      ENVIRONMENT: production
    steps:
      - name: Notify Teams about Deployment Failure
        run: |
          image=$(curl -s "https://api.github.com/repos/edoardodavini/memes/contents/failure" | jq -r '.[] | select(.type=="file") | .download_url' | shuf -n 1)
          curl -X POST "<TEAMS_WEBHOOK_URL>" \
            -H "Content-Type: application/json" \
            -d '{
              "type": "message",
              "attachments": [
                {
                  "contentType": "application/vnd.microsoft.card.adaptive",
                  "contentUrl": null,
                  "content": {
                    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
                    "type": "AdaptiveCard",
                    "version": "1.5",
                    "body": [
                      {
                        "type": "TextBlock",
                        "text": "Deployment failed",
                        "size": "large",
                        "weight": "bolder"
                      },
                      {
                        "type": "Image",
                        "url": "'"$image"'"
                      }
                    ],
                    "actions": [
                      {
                        "type": "Action.OpenUrl",
                        "title": "View Run details",
                        "url": "${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"
                      },
                      {
                        "type": "Action.OpenUrl",
                        "title": "View Website",
                        "url": "https://www.google.com"
                      }
                    ]
                  }
                }
              ]
            }'
```

You can obviously enrich or change completely the flow as your needs.

---
title: "GitHub の Basic Auth の Deprecation に伴って Homebrew を Personal Token で認証するように変更した"
date: "2020-01-02T00:00:00+09:00"
draft: false
keyword: [ github, homebrew ]
tags: [ github, homebrew ]
categories: [ mainteinance ]
summary: "GitHub の Basic Auth の Deprecation に伴って Homebrew を Personal Token で認証するように変更した"
authors: noissefnoc
menu: posts
---

GitHubから `[GitHub] Deprecation Notice` というタイトルで以下の内容のメールが来た

```
    You recently used a password to access an endpoint through the GitHub API using Homebrew/*************. We will deprecate basic authentication using password to this endpoint soon:
```
※ User Agentは一部マスク

`homebrew` コマンドで Basic Auth を使っていて Personal Access Token に対応してなかった様子。 `man brew` でそれらしいセクションを探すと

```
    HOMEBREW_GITHUB_API_TOKEN
     A  personal access token for the GitHub API, used by Homebrew for features such as brew search. You can create one at https://github.com/settings/tokens. If set, GitHub will allow you a greater number of API requests.
     For more information, see: https://developer.github.com/v3/#rate-limiting
    
     Note: Homebrew doesn't require permissions for any of the scopes.
```

とあったので `Settings > Developer > Personal Access Token` で `homebrew` 用の Token を作成して環境変数にセットした。

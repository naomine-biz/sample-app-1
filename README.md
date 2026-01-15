# EKS ArgoCD PoC Sample App

EKS Capabilities for Argo CD のPoC用サンプルアプリケーション。

## アーキテクチャ

```
GitHub Actions (CI)          EKS Capabilities ArgoCD (CD)
     │                              │
     │ 1. Build & Push to ECR       │
     │ 2. Update manifest           │
     │ 3. Slack通知 (ビルド完了)     │
     │                              │
     └──────────────────────────────┤
                                    │ 4. Git変更検知
                                    │ 5. Spoke クラスターにデプロイ
                                    │
                              CloudWatch Container Insights
                                    │
                                    │ 6. Kubernetes Events検知
                                    │ 7. Slack通知 (デプロイ完了)
```

## セットアップ

### 1. ECRリポジトリ作成

```bash
aws ecr create-repository --repository-name sample-app --region ap-northeast-1
```

### 2. GitHub Secrets設定

- `AWS_ROLE_ARN`: OIDC用IAMロールARN
- `SLACK_WEBHOOK_URL`: Slack Incoming Webhook URL

### 3. GitHub OIDC設定

IAMでGitHub Actions用のOIDCプロバイダーとロールを作成。

### 4. ArgoCD Application作成

ArgoCDのUIから:
- Repository: このリポジトリのURL
- Path: `manifests`
- Cluster: spoke クラスター
- Namespace: `default`

## 開発

`app/index.html` を編集してpushすると、自動でビルド→デプロイされます。

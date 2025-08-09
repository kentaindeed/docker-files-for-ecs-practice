# これは何

## 目的
ECS でLaravel を使用したいので、dockerfile 関連の管理場所

## ディレクトリ構造

```bash
ecs-laravel/
├── README.md                    # このファイル
├── docker-compose.yml           # Docker Compose設定
└── docker/                      # Docker関連ファイル
    ├── app/                     # PHP-FPM (Laravel) コンテナ
    │   ├── Dockerfile           # PHP-FPM Dockerfile
    │   └── zzz-www.conf         # PHP-FPM設定ファイル
    ├── nginx/                   # Nginx コンテナ
    │   ├── Dockerfile           # Nginx Dockerfile
    │   ├── nginx.conf           # Nginx メイン設定
    │   └── default.conf         # Nginx サイト設定 (PHP-FPM 9000ポート通信)
    ├── mysql/                   # MySQL コンテナ
    │   ├── Dockerfile           # MySQL Dockerfile
    │   └── my.cnf               # MySQL カスタム設定
    └── redis/                   # Redis コンテナ
        ├── redis.Dockerfile     # Redis Dockerfile
        └── redis.conf           # Redis 設定ファイル
```

## 主な設定

- **Nginx**: PHP-FPM との通信は9000番ポート (TCP) を使用
- **MySQL**: カスタム設定 (slow query log, innodb設定など) を適用
- **PHP-FPM**: Laravel アプリケーション用の設定
- **Redis**: キャッシュ・セッション用
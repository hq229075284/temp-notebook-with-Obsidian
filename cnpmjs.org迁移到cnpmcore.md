[cnpmjs.org](https://github.com/cnpm/cnpmjs.org/wiki/Deploy)迁移到[cnpmcore](https://github.com/cnpm/cnpmcore)

**cnpmcore配置（主要修改点）**
```typescript
// config.default.ts
import assert from 'assert';
import { randomUUID } from 'crypto';
import { join } from 'path';
import { EggAppConfig, PowerPartial } from 'egg';
import OSSClient from 'oss-cnpm';
import { patchAjv } from '../app/port/typebox';
import { ChangesStreamMode, NOT_IMPLEMENTED_PATH, SyncDeleteMode, SyncMode } from '../app/common/constants';
import { CnpmcoreConfig } from '../app/port/config';

export const cnpmcoreConfig: CnpmcoreConfig = {
  name: 'cnpm',
  hookEnable: false,
  hooksLimit: 20,
  sourceRegistry: 'http://192.168.2.130:9000',
  sourceRegistryIsCNpm: true,
  syncUpstreamFirst: true,
  sourceRegistrySyncTimeout: 1 * 60 * 1000/* 180000 */,
  taskQueueHighWaterSize: 100,
  syncMode: SyncMode.exist,
  syncDeleteMode: SyncDeleteMode.delete,
  syncPackageWorkerMaxConcurrentTasks: 10,
  triggerHookWorkerMaxConcurrentTasks: 10,
  createTriggerHookWorkerMaxConcurrentTasks: 10,
  syncPackageBlockList: [],
  enableCheckRecentlyUpdated: true,
  enableSyncBinary: false,
  syncBinaryFromAPISource: '',
  enableSyncDownloadData: false,
  syncDownloadDataSourceRegistry: '',
  syncDownloadDataMaxDate: '',
  enableChangesStream: false,
  checkChangesStreamInterval: 500,
  changesStreamRegistry: 'https://replicate.npmjs.com',
  changesStreamRegistryMode: ChangesStreamMode.streaming,
  // 私有包服务地址前缀
  registry: process.env.CNPMCORE_CONFIG_REGISTRY || 'http://192.168.2.130:9008' || 'http://localhost:7001',
  alwaysAuth: false,
  allowScopes: [
    '@cnpm',
    '@cnpmcore',
    '@example',
    '@dc',
    '@dc-core',
  ],
  allowPublishNonScopePackage: false,
  allowPublicRegistration: true,
  admins: {
    cnpmcore_admin: 'admin@cnpmjs.org',
    // admins对象的key(键)和用户名对应
    hanq: 'hanq@qq.com',
  },
  enableWebAuthn: !!process.env.CNPMCORE_CONFIG_ENABLE_WEB_AUTHN,
  enableCDN: false,
  cdnCacheControlHeader: 'public, max-age=300',
  cdnVaryHeader: 'Accept, Accept-Encoding',
  enableStoreFullPackageVersionManifestsToDatabase: false,
  enableNpmClientAndVersionCheck: true,
  syncNotFound: false,
  redirectNotFound: true,
  enableUnpkg: true,
  strictSyncSpecivicVersion: false,
  strictValidateTarballPkg: false,
};

export default (appInfo: EggAppConfig) => {
  const config = {} as PowerPartial<EggAppConfig>;

  config.keys = process.env.CNPMCORE_EGG_KEYS || randomUUID();
  config.cnpmcore = cnpmcoreConfig;

  // override config from framework / plugin
  config.dataDir = process.env.CNPMCORE_DATA_DIR || join(appInfo.root, '.cnpmcore');

  config.orm = {
    client: 'mysql',
    database: process.env.CNPMCORE_MYSQL_DATABASE || process.env.MYSQL_DATABASE || 'cnpmcore',
    host: process.env.CNPMCORE_MYSQL_HOST || process.env.MYSQL_HOST || '192.168.2.130',
    port: process.env.CNPMCORE_MYSQL_PORT || process.env.MYSQL_PORT || 3306,
    user: process.env.CNPMCORE_MYSQL_USER || process.env.MYSQL_USER || 'root',
    password: process.env.CNPMCORE_MYSQL_PASSWORD || process.env.MYSQL_PASSWORD || 'oYDZjXY2m!c4m!#M',
    charset: 'utf8mb4',
    logger: {},
  };

  config.redis = {
    client: {
      port: Number(process.env.CNPMCORE_REDIS_PORT || 9007),
      host: process.env.CNPMCORE_REDIS_HOST || '192.168.2.130',
      password: process.env.CNPMCORE_REDIS_PASSWORD || 'dba9f981979f624b0f6e039d61b56b3212c8ed419366cfd25247b1555ee6f9c7',
      db: Number(process.env.CNPMCORE_REDIS_DB || 0),
    },
  };

  config.security = {
    csrf: {
      enable: false,
    },
  };

  config.cors = {
    // allow all domains
    origin: (ctx): string => {
      return ctx.get('Origin');
    },
    credentials: true,
  };

  config.nfs = {
    client: null,
    dir: process.env.CNPMCORE_NFS_DIR || join(config.dataDir, 'nfs'),
  };
  /* c8 ignore next 17 */
  // enable oss nfs store by env values
  if (process.env.CNPMCORE_NFS_TYPE === 'oss') {
    assert(process.env.CNPMCORE_NFS_OSS_BUCKET, 'require env CNPMCORE_NFS_OSS_BUCKET');
    assert(process.env.CNPMCORE_NFS_OSS_ENDPOINT, 'require env CNPMCORE_NFS_OSS_ENDPOINT');
    assert(process.env.CNPMCORE_NFS_OSS_ID, 'require env CNPMCORE_NFS_OSS_ID');
    assert(process.env.CNPMCORE_NFS_OSS_SECRET, 'require env CNPMCORE_NFS_OSS_SECRET');
    config.nfs.client = new OSSClient({
      cdnBaseUrl: process.env.CNPMCORE_NFS_OSS_CDN,
      endpoint: process.env.CNPMCORE_NFS_OSS_ENDPOINT,
      bucket: process.env.CNPMCORE_NFS_OSS_BUCKET,
      accessKeyId: process.env.CNPMCORE_NFS_OSS_ID,
      accessKeySecret: process.env.CNPMCORE_NFS_OSS_SECRET,
      defaultHeaders: {
        'Cache-Control': 'max-age=0, s-maxage=60',
      },
    });
  } else if (process.env.CNPMCORE_NFS_TYPE === 's3') {
    assert(process.env.CNPMCORE_NFS_S3_CLIENT_ENDPOINT, 'require env CNPMCORE_NFS_S3_CLIENT_ENDPOINT');
    assert(process.env.CNPMCORE_NFS_S3_CLIENT_ID, 'require env CNPMCORE_NFS_S3_CLIENT_ID');
    assert(process.env.CNPMCORE_NFS_S3_CLIENT_SECRET, 'require env CNPMCORE_NFS_S3_CLIENT_SECRET');
    assert(process.env.CNPMCORE_NFS_S3_CLIENT_BUCKET, 'require env CNPMCORE_NFS_S3_CLIENT_BUCKET');
    // eslint-disable-next-line @typescript-eslint/no-var-requires
    const S3Client = require('s3-cnpmcore');
    config.nfs.client = new S3Client({
      region: process.env.CNPMCORE_NFS_S3_CLIENT_REGION || 'default',
      endpoint: process.env.CNPMCORE_NFS_S3_CLIENT_ENDPOINT,
      credentials: {
        accessKeyId: process.env.CNPMCORE_NFS_S3_CLIENT_ID,
        secretAccessKey: process.env.CNPMCORE_NFS_S3_CLIENT_SECRET,
      },
      bucket: process.env.CNPMCORE_NFS_S3_CLIENT_BUCKET,
      forcePathStyle: !!process.env.CNPMCORE_NFS_S3_CLIENT_FORCE_PATH_STYLE,
      disableURL: !!process.env.CNPMCORE_NFS_S3_CLIENT_DISABLE_URL,
    });
  }

  config.logger = {
    enablePerformanceTimer: true,
    enableFastContextLogger: true,
    appLogName: process.env.CNPMCORE_APP_LOG_NAME || `${appInfo.name}-web.log`,
    coreLogName: process.env.CNPMCORE_CORE_LOG_NAME || 'egg-web.log',
    agentLogName: process.env.CNPMCORE_AGENT_LOG_NAME || 'egg-agent.log',
    errorLogName: process.env.CNPMCORE_ERROR_LOG_NAME || 'common-error.log',
    outputJSON: Boolean(process.env.CNPMCORE_LOG_JSON_OUTPUT || false),
  };
  if (process.env.CNPMCORE_LOG_DIR) {
    config.logger.dir = process.env.CNPMCORE_LOG_DIR;
  }
  if (process.env.CNPMCORE_LOG_JSON_OUTPUT) {
    config.logger.outputJSON = Boolean(process.env.CNPMCORE_LOG_JSON_OUTPUT);
  }

  config.logrotator = {
    // only keep 1 days log files
    maxDays: 1,
  };

  config.bodyParser = {
    // saveTag will send version string in JSON format
    strict: false,
    // set default limit to 10mb, see https://github.com/npm/npm/issues/12750
    jsonLimit: '10mb',
    // https://github.com/cnpm/cnpmcore/issues/551
    ignore: NOT_IMPLEMENTED_PATH,
  };

  // https://github.com/xiekw2010/egg-typebox-validate#%E5%A6%82%E4%BD%95%E5%86%99%E8%87%AA%E5%AE%9A%E4%B9%89%E6%A0%A1%E9%AA%8C%E8%A7%84%E5%88%99
  config.typeboxValidate = { patchAjv };

  config.httpclient = {
    useHttpClientNext: true,
  };

  config.view = {
    root: join(appInfo.baseDir, 'app/port'),
    defaultViewEngine: 'nunjucks',
  };

  config.customLogger = {
    sqlLogger: {
      file: 'sql.log',
    },
  };

  return config;
};

```

同步cnpmjs.org私有包，[参考](https://github.com/cnpm/cnpmcore/issues/338#issuecomment-1517311122)

1. 在`cnpmcore`中创建`hanq`用户
```
PUT http://127.0.0.1:7001/-/user/org.couchdb.user:hanq

参数：
{
  "type":"user",
  "name":"hanq",
  "password":"123",
  "email":"hanq@qq.com"
}

响应：
{
  "ok": true,
  "id": "org.couchdb.user:hanq",
  "rev": "64df3e6b4b183f05941b4f34",
  "token": "cnpm_DO5Tc13MQhHvLIiuawdXWBBDrcthN790_VXMi7"
}
```


2. 往`cnpmcore`中添加原`cnpmjs.org`仓库的信息
```
POST http://127.0.0.1:7001/-/registry

请求头：
authorization: Bearer cnpm_DO5Tc13MQhHvLIiuawdXWBBDrcthN790_VXMi7

参数：
{
  "host":"192.168.2.130:9000",
  "type":"cnpmjsorg",
  "name":"old-cnpm",
  "changeStream":"https://r.cnpmjs.org/_changes"
}

响应：
{
  "ok": true
}
```

3. 创建同步任务
```
PUT http://127.0.0.1:7001/-/package/@dc/table/syncs

请求头：
authorization: Bearer cnpm_DO5Tc13MQhHvLIiuawdXWBBDrcthN790_VXMi7

参数：
{
  "registryName": "old-cnpm",
  "syncDownloadData": false,  是否同步下载量相关的统计数据，和config.default.ts中的DownloadData配置有关
  "skipDependencies": true,
  "force": true,
  "forceSyncHistory": true
}

响应：
{
  "ok": true
}
```
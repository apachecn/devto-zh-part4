# Nestjs(fastify，multer)。上传和裁剪图像。

> 原文：<https://dev.to/sergey_telpuk/nestjs-fastify-multer-uploading-cropping-image-12b9>

朋友们好！

在这篇文章中，我想向你展示我们如何在 [Multer](https://github.com/expressjs/multer) 的帮助下下载和裁剪图像，同时使用不同的适配器，例如，我将展示两个适配器 FTP，AWS。

开始时，使 Multer 适应嵌套，`main.js`可以看下面:

```
import {NestFactory} from '@nestjs/core';
import {AppModule} from './app.module';
import {INestApplication} from '@nestjs/common';
import fs from 'fs';
import {FastifyAdapter, NestFastifyApplication} from '@nestjs/platform-fastify';
import fmp from 'fastify-multipart';

(async function bootstrap() {
    const fastifyAdapter = new FastifyAdapter({
        http2: true,
        logger: true,
        https: {
            allowHTTP1: true, // fallback support for HTTP1
            key: fs.readFileSync(APP.HTTPS_SERVER_KEY),
            cert: fs.readFileSync(APP.HTTPS_SERVER_CRT),
        },
    });

    fastifyAdapter.register(fmp, {
        limits: {
            fieldNameSize: 100, // Max field name size in bytes
            fieldSize: 1000000, // Max field value size in bytes
            fields: 10,         // Max number of non-file fields
            fileSize: 100,      // For multipart forms, the max file size
            files: 1,           // Max number of file fields
            headerPairs: 2000,   // Max number of header key=>value pairs
        },
    });
    const app: INestApplication = await NestFactory.create<NestFastifyApplication>(
        AppModule,
        fastifyAdapter,
    );
    app.enableCors();

    await app.listen(APP.PORT, APP.HOST);
})(); 
```

现在，Nest 可以解析`multipart/form-data`。

在为我们的存储添加一些抽象之后。
创建`UploadImageFactory` :

```
export const UploadImageFactory: FactoryProvider = {
    provide: 'IUploadImage',
    useFactory: () => {
        return StorageFactory.createStorageFromType(TYPE_STORAGE);
    },
}; 
```

`IUploadImage`看起来像:

```
export interface IUploadImage {
    /**
     *
     * @param value
     */
    setFilename(value: string);

    /**
     *
     * @param value
     */
    setCroppedPrefix(value: string): IUploadImage;

    /**
     *
     * @param value
     */
    setCroppedPayload(value: CropQueryDto): IUploadImage;

    getMulter(): any;
} 
```

`StorageFactory`看起来像:

```
export class StorageFactory {
    static createStorageFromType(type: string): IUploadImage {
        switch (type) {
            case TYPE_STORAGE.FTP:
                return new FtpStorageAdapter({
                        fileFilter(req, file, cb) {
                            //TODO validate files on mime-type

                            cb(null, true);
                        },
                    },
                );
            case TYPE_STORAGE.AWS: {
                return new AwsStorageAdapter({
                    fileFilter(req, file, cb) {
                          //TODO validate files on mime-type

                        cb(null, true);
                    },
                });
            }
            default:
                return null;
        }
    }
} 
```

为 FTP 创建第一个适配器:
`FtpStorageAdapter`

```
import FtpStorage from 'multer-ftp';
import fs from 'fs';
import path from 'path';
import {Options, StorageEngine} from 'fastify-multer/lib/interfaces';
import multer from 'fastify-multer';

export class FtpStorageAdapter extends StorageAbstract implements StorageEngine {

    private readonly storage;
    private readonly storageForCropping;

    constructor(options: Options | undefined) {
        super();

        this.setMulter(multer(
            {
                ...options,
                storage: this,
            },
        ).single('file'));

        this.storage = new FtpStorage({...FTP_STORAGE});
        this.storageForCropping = new FtpStorage({...FTP_STORAGE});
    }

    async _handleFile(req, file, cb) {
        const filePath = await this.saveAsTemp(file);

        await this.resize(filePath).then((resizedFile) => {
            this.storageForCropping.opts.destination = (inReq, inFile, inOpts, inCb) => {
                inCb(null, this.croppedPrefix + this.filename + path.extname(inFile.originalname));
            };
            this.storageForCropping._handleFile(req, {
                ...file,
                stream: fs.createReadStream(resizedFile as string),
            }, (err, destination) => {
                if (err) {
                    Promise.reject(err);
                }
                Promise.resolve(true);
            });
        });

        const storage: any = await new Promise((resolve, reject) => {
            this.storage.opts.destination = (inReq, inFile, inOpts, inCb) => {
                inCb(null, this.filename + path.extname(inFile.originalname));
            };
            this.storage._handleFile(req,
                {
                    ...file,
                    stream: fs.createReadStream(filePath as string),
                },
                (err, destination) => {
                    resolve(() => cb(err, destination));
                });
        });

        this.reset();

        storage();
    }

    async _removeFile(req, file, cb) {
        this.reset();
    }
} 
```

为 AWS 创建第二个适配器:
`AwsStorageAdapter`

```
import AwsStorage from 'multer-s3';
import fs from 'fs';
import AwsS3 from 'aws-sdk/clients/s3';
import path from 'path';
import {Options, StorageEngine} from 'fastify-multer/lib/interfaces';
import {StorageAbstract} from '../storage.abstract';
import multer from 'fastify-multer';

export class AwsStorageAdapter extends StorageAbstract implements StorageEngine {

    private readonly storage;
    private readonly storageForCropping;

    private readonly AWS_CONFIG = {
        s3: new AwsS3({
            credentials: {
                accessKeyId: AWS_STORAGE.AWS_ACCESS_KEY_ID,
                secretAccessKey: AWS_STORAGE.AWS_SECRET_ACCESS_KEY,
            },
            s3ForcePathStyle: AWS_STORAGE.AWS_S3_FORCE_PATH_STYLE,
            s3BucketEndpoint: AWS_STORAGE.AWS_S3_BUCKET_ENDPOINT,
            endpoint: AWS_STORAGE.AWS_ENDPOINT,
        }),
        acl: AWS_STORAGE.AWS_ACL,
        bucket: AWS_STORAGE.AWS_BUCKET,
    };

    constructor(options: Options | undefined) {
        super();

        this.setMulter(multer(
            {
                ...options,
                storage: this,
            },
        ).single('file'));

        this.storage = new AwsStorage({
            ...this.AWS_CONFIG,
        });

        this.storageForCropping = AwsStorage({
            ...this.AWS_CONFIG,
        });
    }

    async _handleFile(req, file, cb) {

        const filePath = await this.saveAsTemp(file);

        await this.resize(filePath).then((resizedFile) => {
            this.storageForCropping.getKey = (inReq, inFile, inCb) => {
                inCb(null, this.croppedPrefix + this.filename + path.extname(inFile.originalname));
            };

            this.storageForCropping.getContentType = (inReq, inFile, inCb) => {
                inCb(null, inFile.mimetype);
            };

            this.storageForCropping.getMetadata = (inReq, inFile, inCb) => {
                inCb(null, {fieldName: inFile.fieldname});
            };

            this.storageForCropping._handleFile(req, {
                ...file,
                stream: fs.createReadStream(resizedFile as string),
            }, (err, destination) => {
                if (err) {
                    Promise.reject(err);
                }
                Promise.resolve(true);
            });
        });

        const storage: any = await new Promise((resolve, reject) => {
            this.storage.getKey = (inReq, inFile, inCb) => {
                inCb(null, this.filename + path.extname(inFile.originalname));
            };

            this.storage.getContentType = (inReq, inFile, inCb) => {
                inCb(null, inFile.mimetype);
            };

            this.storage.getMetadata = (inReq, inFile, inCb) => {
                inCb(null, {fieldName: inFile.fieldname});
            };

            this.storage._handleFile(req,
                {
                    ...file,
                    stream: fs.createReadStream(filePath as string),
                },
                (err, destination) => {
                    resolve(() => cb(err, destination));
                });
        });

        this.reset();

        storage();
    }

    async _removeFile(req, file, cb) {
        this.reset();
    }
} 
```

`StorageAbstract`看起来像:

```
import fs from 'fs';
import sharp from 'sharp';

export abstract class StorageAbstract implements IUploadImage {

    protected filename: string;
    protected croppedPayload: CropQueryDto;
    protected croppedPrefix: string;

    private multer: any;

    protected constructor() {
    }

    protected setMulter(multer: any) {
        this.multer = multer;
    }

    setFilename(value): IUploadImage {
        this.filename = value;
        return this;
    }

    setCroppedPrefix(value: string): IUploadImage {
        this.croppedPrefix = value;
        return this;
    }

    setCroppedPayload(value: CropQueryDto): IUploadImage {
        this.croppedPayload = value;
        return this;
    }

    getMulter(): any {
        return this.multer;
    }

    protected async saveAsTemp(file): Promise<string> {
        return new Promise((resolve, reject) => {
            const tmpFile = '/tmp/' + uuid4();
            const writeStream = fs.createWriteStream(tmpFile);
            file.stream
                .pipe(writeStream)
                .on('error', error => reject(error))
                .on('finish', () => resolve(tmpFile));
        });
    }

    protected async resize(file: string): Promise<string> {
        return new Promise((resolve, reject) => {
            const tmpFile = '/tmp/' + uuid4();
            let readStream = fs.createReadStream(file as string);
            const writeStream = fs.createWriteStream(tmpFile);

            if (Object.keys(this.croppedPayload).length !== 0) {
                const {cw, ch, cl, ct} = this.croppedPayload;
                readStream = readStream.pipe(sharp().extract({
                    left: cl,
                    top: ct,
                    width: cw,
                    height: ch,
                }));
            }

            readStream
                .pipe(writeStream)
                .on('error', error => reject(error))
                .on('finish', () => resolve(tmpFile));

        });
    }

    protected reset() {
        this.setFilename(null);
        this.setCroppedPrefix(null);
        this.setCroppedPayload({
            ch: 0,
            cl: 0,
            cw: 0,
            ct: 0,
        });
    }
} 
```

将`IUploadImage`添加到一个虚构的控制器:

```
...
import {BadRequestException, Post,Req,Res} from '@nestjs/common';
...
    constructor(
        @Inject('IUploadImage')
        private readonly uploadImage: IUploadImage,
    ) {

    }
    @Post('/')
    async upload(
        @Query() avatarCropDto: CropQueryDto,
        @Req() req,
        @Res() res,
....
    ): Promise<void> {
        try {
...
             return new Promise((resolve, reject) => {
               this.uploadImage
                .setFilename(uuid)
                .setCroppedPrefix(croppedPrefix)
                .setCroppedPayload(cropPayload)
                .getMulter()(req, res, (err) => {
                    if (err) {
                        reject(err);
                    }
                    resolve(req.file);
                });
        });
        } catch (e) {
            throw new BadRequestException(e.message);
        }
    } 
```

经过这些操作后，我们不仅可以下载，还可以裁剪图像。

[做过知识库。](https://github.com/sergey-telpuk/multer-crop-image)
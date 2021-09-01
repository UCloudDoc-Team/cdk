# 使用 Terraform CDK C# 管理 UCloud 云上资源

## 索引

- [示例代码](https://github.com/hashicorp/terraform-cdk/tree/main/examples/csharp/ucloud)
- [学习资源](https://learn.hashicorp.com/tutorials/terraform/cdktf)
- [参数文档](https://registry.terraform.io/providers/ucloud/ucloud/latest/docs)

## 操作步骤

### 1. 下载示例

```shell
git clone https://github.com/hashicorp/terraform-cdk.git
cd terraform-cdk/examples/csharp/ucloud/
```

### 2. 查看示例

打开示例文件

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using ucloud;
using Constructs;
using HashiCorp.Cdktf;


namespace MyCompany.MyApp
{
    class MyApp : TerraformStack
    {
        public MyApp(Construct scope, string id) : base(scope, id)
        {
            new UcloudProvider(this, "ucloud", new UcloudProviderConfig {
                Region = "cn-bj2",
                ProjectId = System.Environment.GetEnvironmentVariable("UCLOUD_PROJECT_ID") ?? "",
            });

            DataUcloudImages images = new DataUcloudImages(this, "images", new DataUcloudImagesConfig {
	        AvailabilityZone = "cn-bj2-04",
                NameRegex        = "^CentOS 8.2 64",
                ImageType        = "base",	    
            });

	    new Instance(this, "web", new InstanceConfig {
                AvailabilityZone = "cn-bj2-04",
                ImageId = images.Images("0").Id,
                InstanceType = "n-basic-2",
                RootPassword = "wA1234567",
                Name = "cdktf-example-instance",
                Tag = "tf-example",
                BootDiskType = "cloud_ssd",
            });
        }

        public static void Main(string[] args)
        {
            App app = new App();
            new MyApp(app, "ucloud");
            app.Synth();
            Console.WriteLine("App synth complete");
        }
    }
}
```


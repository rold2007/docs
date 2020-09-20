# Image Caches

ImageSharp.Web caches the result of any valid processing operation to allow the fast retrieval of future identical requests. The cache is smart, storing additional metadata to allow the detection of updated source images and can be configured to a fine degree to determine the duration a processed image should be cached for.
  
>[!NOTE]
>It is possible to configure your own image cache by implementing and registering your own version of the @"SixLabors.ImageSharp.Web.Caching.IImageCache" interface.

The following caches are available for the middleware.

### PhysicalFileSystemCache

The @"SixLabors.ImageSharp.Web.Caching.PhysicalFileSystemCache" stores processed image files in the [web root](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/?view=aspnetcore-3.1&tabs=macos#web-root) folder. This is the default cache installed when configuring the middleware.    
  
Images are cached in separate folders based upon a hash of the request URL. this allows the caching of millions of image files without slowing down the file system.
  

### AzureBlobStorageImageCache  
  
This cache allows the caching of image files using [Azure Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/) and is available as an external package installable via [NuGet](https://www.nuget.org/packages/SixLabors.ImageSharp.Web.Providers.Azure)

# [Package Manager](#tab/tabid-1)

```bash
PM > Install-Package SixLabors.ImageSharp.Web.Providers.Azure -Version VERSION_NUMBER
```

# [.NET CLI](#tab/tabid-2)

```bash
dotnet add package SixLabors.ImageSharp.Web.Providers.Azure --version VERSION_NUMBER
```

# [PackageReference](#tab/tabid-3)

```xml
<PackageReference Include="SixLabors.ImageSharp.Web.Providers.Azure" Version="VERSION_NUMBER" />
```

# [Paket CLI](#tab/tabid-4)

```bash
paket add SixLabors.ImageSharp.Web.Providers.Azure --version VERSION_NUMBER
```

***

Once installed the cache @SixLabors.ImageSharp.Web.Caching.Azure.AzureBlobStorageCacheOptions can be configured as follows:


```c#  
// Configure and register the container.  
// Alteratively use `appsettings.json` to represent the class and bind those settings.
.Configure<AzureBlobStorageCacheOptions>(options =>
{
    options.ConnectionString = {AZURE_CONNECTION_STRING};
    options.ContainerName = {AZURE_CONTAINER_NAME};
    
    // Optionally create the cache container on startup if not already created.
    AzureBlobStorageCache.CreateIfNotExists(options, PublicAccessType.None);
})
.SetCache<AzureBlobStorageImageCache>()
```

Images are cached using a hash of the request URL as the blob name. All appropriate metadata is stored in the blob properties to correctly serve the blob with the correct response headers.
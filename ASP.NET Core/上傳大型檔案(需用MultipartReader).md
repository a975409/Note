
```C#
[HttpPost]
[DisableFormValueModelBinding]
public async Task<IActionResult> UploadLarge()
{
   var boundary = MultipartRequestHelper.GetBoundary(
       MediaTypeHeaderValue.Parse(Request.ContentType), 1024);
   var reader = new MultipartReader(boundary, Request.Body);
   MultipartSection section;
   while ((section = await reader.ReadNextSectionAsync()) != null)
   {
       var hasFile = ContentDispositionHeaderValue.TryParse(
           section.ContentDisposition, out var contentDisposition)
           && MultipartRequestHelper.HasFileContentDisposition(contentDisposition);
       if (hasFile)
       {
           var filePath = Path.Combine(_env.WebRootPath, "Uploads", Path.GetRandomFileName());
           using var targetStream = System.IO.File.Create(filePath);
           await section.Body.CopyToAsync(targetStream);
       }
   }
   return Ok();
}
```
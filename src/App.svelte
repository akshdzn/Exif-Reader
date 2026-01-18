  <script>
    import { getAllTags } from 'exif-js';
    import ExifReader, { errors } from 'exifreader';

    let imgInput;
    let exifData = null;

    $: if (imgInput && imgInput.length > 0) {
      const img = imgInput[0];
      ExifReader.load(img).then((tags)=>{exifData = tags});
    }
  </script>

  <main>
    <input type="file" id="imageInput" accept="image/*" bind:files={imgInput}>
    {#if exifData}
      <pre>{JSON.stringify(exifData)}</pre>
    {/if}
  </main>
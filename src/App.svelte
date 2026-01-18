<script>
    import { getAllTags } from "exif-js";
    import ExifReader, { errors } from "exifreader";

    import { UploadSimple, FileImage, Share, Clipboard } from "phosphor-svelte";

    let inputBox;
    let imgInput;
    let exifData = null;

    $: imgSrc =
        imgInput && imgInput.length > 0
            ? URL.createObjectURL(imgInput[0])
            : null;

    $: if (imgInput && imgInput.length > 0) {
        const img = imgInput[0];
        ExifReader.load(img).then((tags) => {
            exifData = tags;
        });
    }
</script>

<main>
    <div class="title-bx">
        <div class="title-border">EXIF Reader</div>
        <div class="title">EXIF Reader</div>
    </div>

    <input
        bind:this={inputBox}
        type="file"
        id="imageInput"
        accept="image/*"
        bind:files={imgInput}
        class="fileInput"
    />
    <button
        class="fileInputButton"
        on:click={() => {
            inputBox.click();
        }}
    >
        <UploadSimple size={24}></UploadSimple>
        Upload image
    </button>

    <button
        class="image-viewer"
        on:click={() => {
            inputBox.click();
        }}
    >
        {#if imgSrc}
            <img src={imgSrc} alt="img" class="img" />
        {:else}
            <div class="image-viewer-alert">
                <FileImage size={43}></FileImage>
                Upload an image to view its metadata
            </div>
        {/if}
    </button>

    <div class="list-bx">
        {#if exifData}
            {#each Object.entries(exifData) as key}
                <div class="list-item">
                    <div class="list-item-name">{key[0]}</div>
                    <div class="list-item-value">{key[1].value}</div>
                </div>
            {/each}
        {/if}
    </div>

    <div class="buttons">
        <button aria-label="export" class="button">
            <Share size={24}></Share>
            export
        </button>
        <button aria-label="copy" class="button">
            <Clipboard size={24}></Clipboard>
            copy
        </button>
    </div>
</main>

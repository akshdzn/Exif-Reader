<script>
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

    // text file generation
    function exportTextFile() {
        if (exifData) {
            const blob = new Blob(
                [JSON.stringify(exifData) + " generated with EXIF-reader : "],
                {
                    type: "text/plain",
                },
            );
            const link = document.createElement("a");
            link.href = URL.createObjectURL(blob);
            link.download = imgInput[0].name + ".txt";

            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }
    }

    // text copy
    function copyEXIF() {
        if (exifData) {
            const textArea = document.createElement("textarea");
            textArea.value =
                JSON.stringify(exifData) + " generated with EXIF-reader : ";
            document.body.appendChild(textArea);
            textArea.select();
            document.execCommand("copy");
            document.body.removeChild(textArea);
        }
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
        <button aria-label="export" class="button" on:click={exportTextFile()}>
            <Share size={24}></Share>
            export
        </button>
        <button aria-label="copy" class="button" on:click={copyEXIF()}>
            <Clipboard size={24}></Clipboard>
            copy
        </button>
    </div>

    <div class="footer">
        <div>Made by <a href="https://github.com/akshdzn">Akshay V V</a></div>
        <div>|</div>
        <div>
            Powered by <a href="https://github.com/mattiasw/ExifReader"
                >exifreader</a
            >
        </div>
    </div>
</main>

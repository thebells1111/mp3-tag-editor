<script>
  const MP3Tag = window.MP3Tag;
  const ID3Writer = window.ID3Writer;
  import { saveAs } from "file-saver";
  let inputFile;
  let blob;
  function handleChange() {
    if (this.files.length === 0) {
      return;
    }
    const reader = new FileReader();
    reader.onload = function () {
      const buffer = this.result;

      // MP3Tag Usage
      const mp3tag = new MP3Tag(buffer);
      mp3tag.read();

      // Handle error if there's any
      if (mp3tag.error !== "") throw new Error(mp3tag.error);
      else console.log(mp3tag.tags);

      const writer = new ID3Writer(buffer);
      writer
        .setFrame("TIT2", "Home")
        .setFrame("TPE1", ["Eminem", "50 Cent"])
        .setFrame("TALB", "Friday Night Lights")
        .setFrame("TYER", 2004)
        .setFrame("TRCK", "6/8")
        .setFrame("TCON", ["Soundtrack"])
        .setFrame("TBPM", 128)
        .setFrame("WPAY", "https://google.com")
        .setFrame("TKEY", "Fbm")
        .setFrame("TXXX", {
          description: "V4V",
          value: JSON.stringify({
            address:
              "030a58b8653d32b99200a2334cfe913e51dc7d155aa0116c176657a4f1722677a3",
            customKey: "696969",
            customValue: "eChoVKtO1KujpAA5HCoB",
          }),
        });

      writer.addTag();
      const taggedSongBuffer = writer.arrayBuffer;
      blob = writer.getBlob();
    };

    if (this.files.length > 0) {
      reader.readAsArrayBuffer(this.files[0]);
    }
  }

  function save() {
    saveAs(blob, "4.mp3");
  }
</script>

<input
  type="file"
  bind:this={inputFile}
  id="input-file"
  on:change={handleChange}
  accept="audio/mpeg"
/>

<button on:click={save}>Save MP3</button>

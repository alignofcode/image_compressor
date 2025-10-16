<script lang="ts">
	import "../app.css";

	import "iconify-icon";
    import Compressor from "compressorjs";
	import Dropzone from "svelte-file-dropzone";

    let files: FileList;
	let urls = [];
	let imgs = [];

	function handleFilesSelect(e: Event) {
	    const { acceptedFiles, fileRejections } = e.detail;
		let preview = document.getElementById("preview")

		for (let accepted_file of acceptedFiles) {
			let downloadable = document.createElement('span');
			downloadable.className = "group relative flex h-48 items-end overflow-hidden rounded-lg bg-gray-100 shadow-lg md:h-80";

			let image_preview = document.createElement('img');
			image_preview.src = URL.createObjectURL(accepted_file);
			image_preview.className = "absolute inset-0 h-full w-full object-cover object-center transition duration-200 group-hover:scale-110";
			image_preview.loading = "lazy";

			let div_effect = document.createElement('div');
			div_effect.className = "pointer-events-none absolute inset-0 bg-gradient-to-t from-gray-800 via-transparent to-transparent opacity-50";

			let link_span = document.createElement('span');
			link_span.className="relative ml-4 mb-3 inline-block text-sm text-white md:ml-5 md:text-lg";

			let webp  = document.createElement('a');
			webp.className = "join-item rounded-none btn";
			webp.innerHTML = '<span class="loading loading-bars loading-xs"></span>';
			let jpeg = document.createElement('a');
			jpeg.className = "join-item rounded-none btn";
			jpeg.innerHTML = '<span class="loading loading-bars loading-xs"></span>';
			let png  = document.createElement('a');
			png.className = "join-item rounded-none btn";
			png.innerHTML  = '<span class="loading loading-bars loading-xs"></span>';

			link_span.appendChild(webp);
			link_span.appendChild(jpeg);
			link_span.appendChild(png);

			downloadable.appendChild(image_preview);
			downloadable.appendChild(div_effect);
			downloadable.appendChild(link_span);

			preview.appendChild(downloadable);

			new Compressor(accepted_file, {
                quality: 0.6,
                width: 2048,
                height: 2048,
				maxWidth: 2048,
				maxHeight: 2048,
                resize: "cover",
				mimeType: 'image/webp',
                success(result: File | Blob) {
                    let file: File;
                    let name = (result as File).name;
                    let type = (result as File).type;
    
                    if (result instanceof Blob) {
                        file = new File([result], "compressed_" + name, { type });
                        console.log(file);
                    } else {
                        file = result as File;
                    }
                    
					let preview = document.getElementById("preview")
					let smaller_file = file.size < accepted_file.size;
					let url = URL.createObjectURL(smaller_file ? file : accepted_file);

					if (!smaller_file) {
						console.log("Compression made image larger...whoops!");
					}

					urls.push(url);
					let img = document.createElement('img');
					img.src = url;
					let a = document.createElement('a');
					a.href = url;
					a.download = name;
					a.appendChild(img);
					
					imgs.push(a);
					webp.href = url;
					webp.download = name;
					webp.innerHTML = "WEBP ";
                },
    
                error(err: Error) {
                    console.log(err.message);
                },
            });

			new Compressor(accepted_file, {
                quality: 0.6,
                width: 2048,
                height: 2048,
				maxWidth: 2048,
				maxHeight: 2048,
                resize: "cover",
				mimeType: 'image/jpeg',
                success(result: File | Blob) {
                    let file: File;
                    let name = (result as File).name;
                    let type = (result as File).type;
    
                    if (result instanceof Blob) {
                        file = new File([result], "compressed_" + name, { type });
                        console.log(file);
                    } else {
                        file = result as File;
                    }
                    
					let preview = document.getElementById("preview")
					let smaller_file = file.size < accepted_file.size;
					let url = URL.createObjectURL(smaller_file ? file : accepted_file);

					if (!smaller_file) {
						console.log("Compression made image larger...whoops!");
					}

					urls.push(url);
					let img = document.createElement('img');
					img.src = url;
					let a = document.createElement('a');
					a.href = url;
					a.download = name;
					a.appendChild(img);

					imgs.push(a);
					jpeg.href = url;
					jpeg.download = name;
					jpeg.innerHTML = "JPEG ";
                },
    
                error(err: Error) {
                    console.log(err.message);
                },
            });

			new Compressor(accepted_file, {
                quality: 0.6,
                width: 2048,
                height: 2048,
				maxWidth: 2048,
				maxHeight: 2048,
                resize: "cover",
				mimeType: 'image/png',
                success(result: File | Blob) {
                    let file: File;
                    let name = (result as File).name;
                    let type = (result as File).type;
    
                    if (result instanceof Blob) {
                        file = new File([result], "compressed_" + name, { type });
                        console.log(file);
                    } else {
                        file = result as File;
                    }
                    
					let preview = document.getElementById("preview")
					let smaller_file = file.size < accepted_file.size;
					let url = URL.createObjectURL(smaller_file ? file : accepted_file);

					if (!smaller_file) {
						console.log("Compression made image larger...whoops!");
					}

					urls.push(url);
					let img = document.createElement('img');
					img.src = url;
					let a = document.createElement('a');
					a.href = url;
					a.download = name;
					a.appendChild(img);
					
					imgs.push(a);
					png.href = url;
					png.download = name;
					png.innerHTML=" PNG ";
                },
    
                error(err: Error) {
                    console.log(err.message);
                },
            });
		}
	}

	function handleonload() {
	}

	function dragover(e) {
		e.preventDefault();
	}

	function dragleave(e) {
		e.preventDefault();
	}

	function drop(e) {
		e.preventDefault();
		let fileinput = document.querySelector("#dropzone-file");
		fileinput.displatchEvent(new Event('change', {'bubbles': false}));
	}
</script>
<svelte:window on:load="{()=>handleonload()}"/>

<div class="navbar bg-base-100">
  Compress Images
</div>

<Dropzone on:drop={handleFilesSelect} accept='image/*'>
	<iconify-icon height="2em" icon="uil:image-upload" class="pt-32"></iconify-icon>
	<p class="mb-2 text-sm text-gray-500 dark:text-gray-400">
		<span class="font-semibold">Click to upload</span> or drag and drop
	</p>
	<p class="text-xs text-gray-500 dark:text-gray-400 pb-32">SVG, PNG, JPG or WEBP (MAX. 2048x2048)</p>
</Dropzone>

<div class="bg-white dark:bg-gray-800 h-screen h-full py-6 sm:py-8 lg:py-12">
    <div class="mx-auto max-w-screen-2xl px-4 md:px-8">
		<!-- images - start -->
        <div class="grid grid-cols-2 gap-4 sm:grid-cols-3 md:gap-6 xl:gap-8" id="preview">
        </div>
		 <!-- images - end -->
    </div>
</div>

<template>
  <div>
    <div class="album">
      <div class="thumbnail" v-for="image in filteredImages" :key="image.id">
        <div
          class="image"
          :style="`background-image:url('${image.thumbnail || image.src}')`"
          @click="openImage(image.id)"
          :alt="image.title"
        />
        <p style="margin-top: 0.5rem; margin-bottom: 0">
          {{ image.title }}
        </p>
        <p style="font-size: 12px">
          (download
          <a :href="image.src" target="_blank" rel="noopener noreferrer"
            >image</a
          >
          <span v-if="image.source?.url"
            >,
            <a
              :href="image.source.url"
              target="_blank"
              rel="noopener noreferrer"
            >
              {{ image.source.name }}
            </a> </span
          >)
        </p>
      </div>
    </div>

    <div class="enlarged" v-if="enlarged" v-on-click-outside="closeImage">
      <a class="open-in-new-tab" @click="openInNewTab" v-if="!isLoading">
        <svg
          clip-rule="evenodd"
          fill-rule="evenodd"
          stroke-linejoin="round"
          stroke-miterlimit="2"
          viewBox="0 0 24 24"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="m22 7c0-.478-.379-1-1-1h-14c-.62 0-1 .519-1 1v14c0 .621.52 1 1 1h14c.478 0 1-.379 1-1zm-14.5.5h13v13h-13zm-5.5 7.5v2c0 .621.52 1 1 1h2v-1.5h-1.5v-1.5zm13.369-5s-2.136.002-4.616.003c-.414 0-.75.336-.75.75-.001 2.479-.003 4.612-.003 4.612 0 .41.335.745.75.745.414 0 .75-.334.75-.745v-2.804l4.439 4.439c.293.293.768.293 1.061 0s.293-.768 0-1.061l-4.439-4.439h2.808c.409 0 .741-.336.741-.75s-.333-.75-.741-.75zm-11.869.637v3.363h-1.5v-3.363zm0-4.637v3.637h-1.5v-3.637zm11.5-4v1.5h1.5v1.5h1.5v-2c0-.478-.379-1-1-1zm-10 0h-2c-.62 0-1 .519-1 1v2h1.5v-1.5h1.5zm4.5 1.5h-3.5v-1.5h3.5zm4.5 0h-3.5v-1.5h3.5z"
            fill-rule="nonzero"
          />
        </svg>
      </a>
      <a class="close" @click="closeImage" v-if="!isLoading">
        <svg
          clip-rule="evenodd"
          fill-rule="evenodd"
          stroke-linejoin="round"
          stroke-miterlimit="2"
          viewBox="0 0 24 24"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="m12.002 2.005c5.518 0 9.998 4.48 9.998 9.997 0 5.518-4.48 9.998-9.998 9.998-5.517 0-9.997-4.48-9.997-9.998 0-5.517 4.48-9.997 9.997-9.997zm0 8.933-2.721-2.722c-.146-.146-.339-.219-.531-.219-.404 0-.75.324-.75.749 0 .193.073.384.219.531l2.722 2.722-2.728 2.728c-.147.147-.22.34-.22.531 0 .427.35.75.751.75.192 0 .384-.073.53-.219l2.728-2.728 2.729 2.728c.146.146.338.219.53.219.401 0 .75-.323.75-.75 0-.191-.073-.384-.22-.531l-2.727-2.728 2.717-2.717c.146-.147.219-.338.219-.531 0-.425-.346-.75-.75-.75-.192 0-.385.073-.531.22z"
            fill-rule="nonzero"
          />
        </svg>
      </a>
      <div class="loader" v-if="isLoading">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          viewBox="0 0 24 24"
        >
          <path
            d="M13 0c3.667.305 6.863 2.26 8.851 5.129l-1.746 1.013c-1.634-2.273-4.182-3.84-7.105-4.133v-2.009zm-11 12c0-1.47.324-2.863.891-4.122l-1.737-1.007c-.733 1.558-1.154 3.292-1.154 5.129 0 1.837.421 3.571 1.153 5.129l1.738-1.008c-.567-1.259-.891-2.651-.891-4.121zm20 0c0 1.47-.324 2.863-.891 4.122l1.738 1.007c.732-1.558 1.153-3.292 1.153-5.129s-.421-3.571-1.153-5.129l-1.738 1.007c.567 1.259.891 2.652.891 4.122zm-1.895 5.858c-1.634 2.273-4.182 3.84-7.105 4.133v2.009c3.667-.305 6.863-2.26 8.851-5.129l-1.746-1.013zm-16.21-11.717c1.634-2.272 4.183-3.839 7.105-4.132v-2.009c-3.667.305-6.862 2.259-8.851 5.128l1.746 1.013zm7.105 15.85c-2.923-.293-5.471-1.86-7.105-4.133l-1.746 1.013c1.988 2.87 5.184 4.824 8.851 5.129v-2.009z"
          />
        </svg>
      </div>
      <img
        :src="selectedImage.src"
        :alt="selectedImage.title"
        :style="
          selectedImage.width && selectedImage.height
            ? `aspect-ratio: ${selectedImage.width} / ${selectedImage.height}; min-width: 100%; min-height: 100%`
            : ''
        "
        @load="onImageLoad"
        @error="onImageLoad"
      />
      <a class="arrow left" @click="prevImage">
        <svg
          clip-rule="evenodd"
          fill-rule="evenodd"
          stroke-linejoin="round"
          stroke-miterlimit="2"
          viewBox="0 0 24 24"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="m12.012 2c5.518 0 9.997 4.48 9.997 9.998 0 5.517-4.479 9.997-9.997 9.997s-9.998-4.48-9.998-9.997c0-5.518 4.48-9.998 9.998-9.998zm-1.523 6.21s-1.502 1.505-3.255 3.259c-.147.147-.22.339-.22.531s.073.383.22.53c1.753 1.754 3.254 3.258 3.254 3.258.145.145.335.217.526.217.192-.001.384-.074.531-.221.292-.293.294-.766.003-1.057l-1.977-1.977h6.693c.414 0 .75-.336.75-.75s-.336-.75-.75-.75h-6.693l1.978-1.979c.29-.289.287-.762-.006-1.054-.147-.147-.339-.221-.53-.222-.19 0-.38.071-.524.215z"
            fill-rule="nonzero"
          />
        </svg>
      </a>
      <a class="arrow right" @click="nextImage" v-if="!isLoading">
        <svg
          clip-rule="evenodd"
          fill-rule="evenodd"
          stroke-linejoin="round"
          stroke-miterlimit="2"
          viewBox="0 0 24 24"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="m12.007 2c-5.518 0-9.998 4.48-9.998 9.998 0 5.517 4.48 9.997 9.998 9.997s9.998-4.48 9.998-9.997c0-5.518-4.48-9.998-9.998-9.998zm1.523 6.21s1.502 1.505 3.255 3.259c.147.147.22.339.22.531s-.073.383-.22.53c-1.753 1.754-3.254 3.258-3.254 3.258-.145.145-.335.217-.526.217-.192-.001-.384-.074-.531-.221-.292-.293-.294-.766-.003-1.057l1.977-1.977h-6.693c-.414 0-.75-.336-.75-.75s.336-.75.75-.75h6.693l-1.978-1.979c-.29-.289-.287-.762.006-1.054.147-.147.339-.221.53-.222.19 0 .38.071.524.215z"
            fill-rule="nonzero"
          />
        </svg>
      </a>
    </div>
  </div>
</template>

<script setup>
import { computed, onMounted, ref } from "vue";
import { vOnClickOutside } from "@vueuse/components";

const images = ref(
  [
    {
      title: "Tekening origineel",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-05-originele-tekening.png",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-05-originele-tekening-thumbnail.jpg",
      width: 1605,
      height: 3320,
      source: {
        url: "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-05-originele-tekening.fml",
        name: "FML",
      },
      tags: ["tekeningen"],
    },
    {
      title: "Schets uitbouw",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-13-tekening.png",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-13-tekening-thumbnail.jpg",
      width: 1569,
      height: 3320,
      source: {
        url: "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-13-tekening.fml",
        name: "FML",
      },
      tags: ["tekeningen"],
    },
    {
      title: "Bouwkundige tekening 2008",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2008-08-18-bouwkundige-tekening.jpg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2008-08-18-bouwkundige-tekening-thumbnail.jpg",
      width: 7100,
      height: 5190,
      tags: ["tekeningen"],
    },
    {
      title: "Kadastrale perceel 2008",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2008-08-18-kadastrale-perceel.jpg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2008-08-18-kadastrale-perceel-thumbnail.jpg",
      width: 7085,
      height: 5190,
      tags: ["tekeningen"],
    },
    {
      title: "Voorkant raam afmetingen",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-05-voorkant-raam-afmetingen.png",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-05-voorkant-raam-afmetingen-thumbnail.png",
      width: null,
      height: null,
      tags: ["tekeningen", "ramen"],
    },
    {
      title: "Voorkant raam voor",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-voor.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-voor-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Voorkant raam links",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-links.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-links-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Voorkant raam links detail",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-links-detail.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-links-detail-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Voorkant raam rechts",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-rechts.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-rechts-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Voorkant raam binnen rechts",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-rechts.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-rechts-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Voorkant raam binnen",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Voorkant raam binnen rechts detail",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-rechts-detail.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-rechts-detail-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Voorkant raam binnen detail",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-detail.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-detail-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Voorkant raam binnen rechts tekst",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-rechts-tekst.jpeg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-09-03-voorkant-raam-binnen-rechts-tekst-thumbnail.jpeg",
      width: null,
      height: null,
      tags: ["ramen"],
    },
    {
      title: "Riolering kruipruimte 1",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-08-riolering-kruipruimte-1.png",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-08-riolering-kruipruimte-1-thumbnail.jpg",
      width: 1852,
      height: 1058,
      tags: ["riolering"],
    },
    {
      title: "Riolering kruipruimte 2",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-08-riolering-kruipruimte-2.png",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-08-riolering-kruipruimte-2-thumbnail.jpg",
      width: 1856,
      height: 1058,
      tags: ["riolering"],
    },
    {
      title: "Riolering kruipruimte 3",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-08-riolering-kruipruimte-3.jpg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-08-riolering-kruipruimte-3-thumbnail.jpg",
      width: 911,
      height: 1280,
      tags: ["riolering"],
    },
    {
      title: "1935 riool tekening",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/1935-riool-tekening.jpg",
      thumbnail:
        "https://files.adriaanvanrossum.nl/curacaostraat/1935-riool-tekening-thumbnail.jpg",
      width: 7768,
      height: 4668,
      tags: ["tekeningen", "riolering"],
    },
    {
      title: "Bouwtechnische keuring",
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2023-08-09-bouwtechnische-keuring.png",
      width: 200,
      height: 200,
      source: {
        url: "https://files.adriaanvanrossum.nl/curacaostraat/bouwtechnische-keuring.pdf",
        name: "PDF 66mb!",
      },
      tags: ["rapporten"],
    },
    {
      title: "Lintvoeg- en vloerwaterpassing",
      width: 200,
      height: 200,
      src: "https://files.adriaanvanrossum.nl/curacaostraat/2008-09-01-lintvoeg-en-vloerwaterpassing.jpg",
      source: {
        url: "https://files.adriaanvanrossum.nl/curacaostraat/2008-09-01-lintvoeg-en-vloerwaterpassing.pdf",
        name: "PDF 2mb",
      },
      tags: ["rapporten"],
    },
  ].map((image, index) => ({ ...image, id: index })),
);

const filteredImages = computed(() => {
  if (props.tag) {
    return images.value.filter((image) => image.tags?.includes(props.tag));
  } else {
    return images.value;
  }
});

const enlarged = ref(false);
const selectedImageId = ref(null);
const isLoading = ref(false);

const props = defineProps({
  tag: {
    type: String,
    required: false,
  },
});

const onImageLoad = () => {
  isLoading.value = false;
};

const selectedImage = computed(() => {
  return filteredImages.value.find(
    (image) => image.id === selectedImageId.value,
  );
});

const openImage = (id) => {
  enlarged.value = true;
  selectedImageId.value = filteredImages.value.find(
    (image) => image.id === id,
  ).id;
  isLoading.value = true;
};

const closeImage = () => {
  enlarged.value = false;
  selectedImageId.value = null;
};

const prevImage = () => {
  const index = filteredImages.value.findIndex(
    (image) => image.id === selectedImageId.value,
  );
  if (index > 0) {
    selectedImageId.value = filteredImages.value[index - 1].id;
  } else {
    selectedImageId.value =
      filteredImages.value[filteredImages.value.length - 1].id;
  }
  isLoading.value = true;
};

const nextImage = () => {
  const index = filteredImages.value.findIndex(
    (image) => image.id === selectedImageId.value,
  );
  console.log({
    date: new Date(),
    index,
    nextImage: selectedImageId.value,
    imagesLength: filteredImages.value.length,
  });
  if (index < filteredImages.value.length - 1) {
    selectedImageId.value = filteredImages.value[index + 1].id;
  } else {
    selectedImageId.value = filteredImages.value[0].id;
  }
  isLoading.value = true;
};

const openInNewTab = () => {
  window.open(selectedImage.value.src, "_blank");
};

onMounted(() => {
  const handleKeyDown = (event) => {
    if (enlarged.value) {
      if (event.code === "ArrowLeft") {
        prevImage();
      } else if (event.code === "ArrowRight") {
        nextImage();
      } else if (event.code === "Escape") {
        closeImage();
      }
    }
  };
  window.addEventListener("keydown", handleKeyDown);
  return () => window.removeEventListener("keydown", handleKeyDown);
});
</script>

<style>
.album {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  --image-width: 200px;
  margin-left: -1rem;
  margin-right: -1rem;
}
@media screen and (max-width: 600px) {
  .album {
    --image-width: calc(100vw / 2 - 3rem);
  }
}
.thumbnail {
  display: flex;
  flex-direction: column;
  width: var(--image-width);
  margin: 1rem;
  text-align: center;
}
.thumbnail .image {
  width: var(--image-width);
  height: var(--image-width);
  background-size: cover;
  background-position: center;
  border-radius: 5px;
  box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  transition: transform 0.2s ease-in-out;
}
@media (hover: hover) {
  .thumbnail .image:hover {
    transform: scale(1.1);
  }
}
.enlarged {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.enlarged img {
  min-width: 200px;
  max-width: calc(100vw - 2rem);
  max-height: calc(100vh - 2rem);
  border-radius: 1rem;
  box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.1);
  background-color: #dedee0;
}
.enlarged .loader,
.enlarged .arrow,
.enlarged .open-in-new-tab,
.enlarged .close {
  position: absolute;
  top: 50%;
  width: 50px;
  height: 50px;
  background-size: 30px 30px;
  background-repeat: no-repeat;
  cursor: pointer;
  transform: translateY(-50%);
  opacity: 0.5;
}
@media (hover: hover) {
  .enlarged .arrow:hover,
  .enlarged .open-in-new-tab:hover,
  .enlarged .close:hover {
    opacity: 1;
    background-color: transparent;
  }
}
.enlarged .close {
  top: 32px;
  right: 10px;
}
.enlarged .open-in-new-tab {
  top: 32px;
  right: 65px;
  /* flip horizontally */
  transform: translate(0, -50%) scaleX(-1);
}
.enlarged .arrow.left {
  left: 10px;
}
.enlarged .arrow.right {
  right: 10px;
}
.enlarged .loader {
  width: 100px;
  height: 100px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

@keyframes rotate {
  100% {
    transform: rotate(360deg);
  }
}

@keyframes fadeIn {
  0% {
    opacity: 0;
  }
  50% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

.enlarged .loader svg {
  animation: rotate 3s linear infinite;
  width: 100px;
  height: 100px;

  animation:
    rotate 3s linear infinite,
    fadeIn 2s;
}
</style>

<script context="module" lang="ts">
  import { createApi } from 'unsplash-js';

	export const prerender = true;

  const { VITE_UNSPLASH_ACCESS_KEY } = import.meta.env
  const unsplash = createApi({ accessKey: VITE_UNSPLASH_ACCESS_KEY });

  export async function load({page}) {
    //console.log(page);
    const { gender, pagenum } = page.params;
    const result = await unsplash.search.getPhotos({
      query: `${gender} profile`,
      page: pagenum,
      perPage: 20,
      //color: 'green',
      //orientation: 'portrait',
    });

    if (result.errors) {
        // handle error here
        console.log('error occurred: ', result.errors[0]);
        return {props: {photos: []}};
    } else {
      // handle success here
      //console.log(result.response.total_pages)
      const urls = result.response.results.map( (result) => {
        return result.urls.raw + '&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80';
      })
      return {props: {
        totalPhotos: result.response.total,
        totalPages: result.response.total_pages,
        photos: urls,
        gender: gender,
        pageNum: parseInt(pagenum)
      }};

    }
  }
</script>

<script lang="ts">
  export let photos;
  export let totalPhotos;
  export let totalPages;
  export let gender;
  export let pageNum;
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section>
  <div class="info">
    Photos: {totalPhotos}
    Pages: {totalPages}
    <a href={`/profiles/${gender}-${pageNum - 1}`}>Prev</a>
    {pageNum}
    <a href={`/profiles/${gender}-${pageNum + 1}`}>Next</a>
  </div>

  <div class="collection">
    {#each photos as photo}
      <img src={photo} />
    {/each}
  </div>

  <div class="">
    <code>
      {#each photos as photo}
        {photo}
        <br />
      {/each}
    </code>
  </div>
</section>

<style>
	.collection {
		display: grid;
    grid-template-columns: repeat(4, minmax(0, 1fr));
	}
</style>
<script context="module" lang="ts">
  import { createApi } from 'unsplash-js';

	export const prerender = true;

  const { VITE_UNSPLASH_ACCESS_KEY } = import.meta.env
  const unsplash = createApi({ accessKey: VITE_UNSPLASH_ACCESS_KEY });

  export async function load({page}) {
  
    const result = await unsplash.search.getPhotos({
      query: 'profile girl',
      page: 1,
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
      //console.log(result.response)
      const urls = result.response.results.map( (result) => {
        return result.urls.raw + '&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80';
      })
      return {props: {photos: urls}};

    }
  }
</script>

<script lang="ts">
  export let photos;
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section>

  {#each photos as photo}
    <img src={photo} />
  {/each}

</section>

<style>
	section {
		display: grid;
    grid-template-columns: repeat(4, minmax(0, 1fr));
	}
</style>

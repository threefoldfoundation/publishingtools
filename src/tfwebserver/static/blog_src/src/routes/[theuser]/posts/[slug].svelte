<script context="module">
  import { getPosts, getMetadata } from "../../_api.js";

  export async function preload({ params, query }) {
    // the `slug` parameter is available because
    // this file is called [slug].svelte
    let blogName = params.theuser;
    let slug = params.slug;
    const metadata = await getMetadata(blogName);
    let posts = await getPosts(blogName);
    const lookup = new Map();
    posts.forEach((post, idx) => {
      post.prev = { slug: undefined, title: undefined };
      post.next = { slug: undefined, title: undefined };
      if (idx > 0) {
        let prev_slug = posts[idx - 1].slug;
        let prev_title = posts[idx - 1].title;
        post.prev = { slug: prev_slug, title: prev_title };
      }
      if (idx < posts.length && posts[idx + 1]) {
        let next_slug = posts[idx + 1].slug;
        let next_title = posts[idx + 1].title;
        post.next = { slug: next_slug, title: next_title };
      }
      lookup.set(post.slug, JSON.stringify(post));
    });
    let post = JSON.parse(lookup.get(slug));
    console.log("post", post);
    return { post, metadata };
  }
</script>

<script>
  export let post;
  console.log("component post", post);
  export let metadata;
  const { preloading, page, session } = stores();
  import { stores } from "@sapper/app";

  import Post from "../../../components/Post.svelte";
</script>

<style>
  /*
		By default, CSS is locally scoped to the component,
		and any unused styles are dead-code-eliminated.
		In this page, Svelte can't know which elements are
		going to appear inside the {{{page.html}}} block,
		so we have to use the :global(...) modifier to target
		all elements inside .content
	*/
  .content :global(h2) {
    font-size: 1.4em;
    font-weight: 500;
  }

  .content :global(pre) {
    background-color: #f9f9f9;
    box-shadow: inset 1px 1px 5px rgba(0, 0, 0, 0.05);
    padding: 0.5em;
    border-radius: 2px;
    overflow-x: auto;
  }

  .content :global(pre) :global(code) {
    background-color: transparent;
    padding: 0;
  }

  .content :global(ul) {
    line-height: 1.5;
  }

  .content :global(li) {
    margin: 0 0 0.5em 0;
  }
</style>

<svelte:head>
  <title>{post.title}</title>

  <link
    rel="stylesheet"
    href="//cdn.jsdelivr.net/gh/highlightjs/cdn-release@9.15.8/build/styles/default.min.css" />
</svelte:head>

<Post {post} {metadata} />

<script>
  import { stores } from "@sapper/app";
  import { onMount } from "svelte";
  const { preloading, page } = stores();

  import { fade } from "svelte/transition";

  import AppBar from "components/AppBar";
  import Tabs from "components/Tabs";
  import Button from "components/Button";
  import { Spacer } from "components/Util";
  import List from "components/List";
  import ListItem from "components/List/ListItem.svelte";
  import NavigationDrawer from "components/NavigationDrawer";
  import ProgressLinear from "components/ProgressLinear";

  import {
    right,
    elevation,
    persistent,
    showNav,
    showNavMobile,
    breakpoint
  } from "stores.js";

  let innerWidth = 0;
  let selected = "";

  function calcBreakpoint(width) {
    if (width > 1279) {
      return "xl";
    }
    if (width > 1023) {
      return "lg";
    }
    if (width > 767) {
      return "md";
    }
    return "sm";
  }

  onMount(() => {
    if (!process.browser) return;

    breakpoint.update(() => calcBreakpoint(window.innerWidth));
  });

  function updateBreakpoint({ target }) {
    const bp = calcBreakpoint(target.innerWidth);

    return breakpoint.update(() => bp);
  }

  const menu = [
    { to: "/about", text: "About" },
    { to: "/blog", text: "Blog" },
  ];

  const topMenu = [
    { to: "/about", text: "About" },
    { to: "/blog", text: "Blog" },
  ];

  function toggleNav() {
    return showNavMobile.update(() => !$showNavMobile);
  }

  $: path = $page.path;
</script>

<svelte:head>
  <title>Smelte: Material design using Tailwind CSS for Svelte</title>
</svelte:head>

<svelte:window on:resize={updateBreakpoint} />

{#if $preloading}
  <ProgressLinear app />
{/if}

{#each menu as link}
  <a href={link.to} class="hidden">{link.text}</a>
{/each}

<AppBar>
  <a href="." class="px-2 md:px-8 flex items-center">
    <img src="/logo.png" alt="Smelte logo" width="44" />
    <h6 class="pl-3 text-white tracking-widest font-thin text-lg">SMELTE</h6>
  </a>
  <Spacer />
  <Tabs c="sm:hidden md:flex" items={topMenu} bind:selected={path} />
  <div class="md:hidden">
    <Button icon="menu" small text on:click={toggleNav} />
  </div>
</AppBar>

{#if $breakpoint}
  <main
    class="container relative p-8 lg:max-w-3xl lg:ml-64 mx-auto mb-10 mt-24
    md:ml-56 md:max-w-md md:px-3"
    transition:fade={{ duration: 300 }}>
    <NavigationDrawer
      bind:showDesktop={$showNav}
      bind:showMobile={$showNavMobile}
      right={$right}
      persistent={$persistent}
      elevation={$elevation}
      breakpoint={$breakpoint}>
      <h6 class="p-6 ml-1 pb-2 text-xs text-gray-900">First section of the menu</h6>
      <List items={menu}>
        <span slot="item" let:item class="cursor-pointer">
          {#if item.to === '/typography'}
            <hr />
            <h6 class="p-6 ml-1 py-2 text-xs text-gray-900">Utilities</h6>
          {/if}

          <a href={item.to}>
            <ListItem
              selected={path.includes(item.to)}
              {...item}
              dense
              navigation />
          </a>
        </span>
      </List>
      <hr />
    </NavigationDrawer>

    <slot />

  </main>
{/if}
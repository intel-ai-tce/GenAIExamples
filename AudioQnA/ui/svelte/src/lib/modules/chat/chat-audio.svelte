<!--
  Copyright (c) 2024 Intel Corporation

  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->

<script lang="ts">
	import { onMount } from "svelte";

	export let src: string;

    let audioEl: HTMLAudioElement
    let play = false

    onMount(() => {
        audioEl.addEventListener('ended', () => {
            audioEl.currentTime = 0
            play = false
        })
    })

    function handlePlayClick() {
        if (play === true) {
            audioEl.play()
        } else {
            audioEl.pause()
        }
    }
</script>


<audio class="hidden" bind:this={audioEl} {src} />

<div class="flex">
    <label class="swap">

        <!-- this hidden checkbox controls the state -->
        <input type="checkbox" bind:checked={play} on:change={handlePlayClick} />

        <!-- volume on icon -->
        <svg class="swap-on fill-current w-5 h-5" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"><path d="M512 1024A512 512 0 1 1 512 0a512 512 0 0 1 0 1024z m3.008-92.992a416 416 0 1 0 0-832 416 416 0 0 0 0 832zM320 320h128v384H320V320z m256 0h128v384H576V320z" fill="#bcdbff"></path></svg>
        <!-- volume off icon -->
        <svg class="swap-off fill-current w-5 h-5" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"><path d="M512 1024A512 512 0 1 1 512 0a512 512 0 0 1 0 1024z m3.008-92.992a416 416 0 1 0 0-832 416 416 0 0 0 0 832zM383.232 287.616l384 224.896-384 223.104v-448z" fill="#bcdbff"></path></svg>
    </label>

    <div class="bg-contain bg-left bg-repeat-round w-20 ml-2" class:audio={play} class:default={!play}></div>
</div>

<style>
    .default {
        background-image: url(../../assets/icons/png/audio1.png)
    }
    .audio {
        animation-name: flowingAnimation;
        animation-duration: 3s;
        animation-iteration-count: infinite;
        animation-timing-function: linear;
    }

    @keyframes flowingAnimation {
        0% {
            background-image: url(../../assets/icons/png/audio1.png)
        }

        50% {
            background-image: url(../../assets/icons/png/audio2.png)
        }

        100% {
            background-image: url(../../assets/icons/png/audio1.png)
        }
    }
</style>

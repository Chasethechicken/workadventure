<script lang="ts">
    import { gameManager } from "../../Phaser/Game/GameManager";
    import { onMount } from "svelte";

    let gameScene = gameManager.getCurrentGameScene();

    let expandedMapCopyright = false;
    let expandedTilesetCopyright = false;
    let expandedAudioCopyright = false;

    let mapName: string = "";
    let mapLink: string = "";
    let mapDescription: string = "";
    let mapCopyright: string = "The map creator did not declare a copyright for the map.";
    let tilesetCopyright: string[] = [];
    let audioCopyright: string[] = [];

    onMount(() => {
        if (gameScene.mapFile.properties !== undefined) {
            const propertyName = gameScene.mapFile.properties.find((property) => property.name === "mapName");
            if (propertyName !== undefined && typeof propertyName.value === "string") {
                mapName = propertyName.value;
            }
            const propertyLink = gameScene.mapFile.properties.find((property) => property.name === "mapLink");
            if (propertyLink !== undefined && typeof propertyLink.value === "string") {
                mapLink = propertyLink.value;
            }
            const propertyDescription = gameScene.mapFile.properties.find(
                (property) => property.name === "mapDescription"
            );
            if (propertyDescription !== undefined && typeof propertyDescription.value === "string") {
                mapDescription = propertyDescription.value;
            }
            const propertyCopyright = gameScene.mapFile.properties.find((property) => property.name === "mapCopyright");
            if (propertyCopyright !== undefined && typeof propertyCopyright.value === "string") {
                mapCopyright = propertyCopyright.value;
            }
        }

        for (const tileset of gameScene.mapFile.tilesets) {
            if (tileset.properties !== undefined) {
                const propertyTilesetCopyright = tileset.properties.find(
                    (property) => property.name === "tilesetCopyright"
                );
                if (propertyTilesetCopyright !== undefined && typeof propertyTilesetCopyright.value === "string") {
                    // Assignment needed to trigger Svelte's reactivity
                    tilesetCopyright = [...tilesetCopyright, propertyTilesetCopyright.value];
                }
            }
        }

        for (const layer of gameScene.mapFile.layers) {
            if (layer.type && layer.type === "tilelayer" && layer.properties) {
                const propertyAudioCopyright = layer.properties.find((property) => property.name === "audioCopyright");
                if (propertyAudioCopyright !== undefined && typeof propertyAudioCopyright.value === "string") {
                    // Assignment needed to trigger Svelte's reactivity
                    audioCopyright = [...audioCopyright, propertyAudioCopyright.value];
                }
            }
        }
    });
</script>

<div class="about-room-main">
    <h2>Information on the map</h2>
    <section class="container-overflow">
        <h3>{mapName}</h3>
        <p class="string-HTML">{mapDescription}</p>
        {#if mapLink}
            <p class="string-HTML">&gt; <a href={mapLink} target="_blank">link to this map</a> &lt;</p>
        {/if}
        <h3 class="nes-pointer hoverable" on:click={() => (expandedMapCopyright = !expandedMapCopyright)}>
            Copyrights of the map
        </h3>
        <p class="string-HTML" hidden={!expandedMapCopyright}>{mapCopyright}</p>
        <h3 class="nes-pointer hoverable" on:click={() => (expandedTilesetCopyright = !expandedTilesetCopyright)}>
            Copyrights of the tilesets
        </h3>
        <section hidden={!expandedTilesetCopyright}>
            {#each tilesetCopyright as copyright}
                <p class="string-HTML">{copyright}</p>
            {:else}
                <p>
                    The map creator did not declare a copyright for the tilesets. This doesn't mean that those tilesets
                    have no license.
                </p>
            {/each}
        </section>
        <h3 class="nes-pointer hoverable" on:click={() => (expandedAudioCopyright = !expandedAudioCopyright)}>
            Copyrights of audio files
        </h3>
        <section hidden={!expandedAudioCopyright}>
            {#each audioCopyright as copyright}
                <p class="string-HTML">{copyright}</p>
            {:else}
                <p>
                    The map creator did not declare a copyright for audio files. This doesn't mean that those tilesets
                    have no license.
                </p>
            {/each}
        </section>
    </section>
</div>

<style lang="scss">
    .string-HTML {
        white-space: pre-line;
    }

    div.about-room-main {
        height: calc(100% - 56px);

        h2,
        h3 {
            width: 100%;
            text-align: center;
        }

        h3.hoverable:hover {
            background-color: #3c3e40;
            border-radius: 32px;
        }

        section.container-overflow {
            height: calc(100% - 220px);
            margin: 0;
            padding: 0;
            overflow-y: auto;
        }
    }

    @media only screen and (max-width: 800px), only screen and (max-height: 800px) {
        div.about-room-main {
            section.container-overflow {
                height: calc(100% - 120px);
            }
        }
    }
</style>

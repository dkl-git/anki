<!--
Copyright: Ankitects Pty Ltd and contributors
License: GNU AGPL, version 3 or later; http://www.gnu.org/licenses/agpl.html
-->
<script lang="ts">
    import Container from "../components/Container.svelte";
    import Col from "../components/Col.svelte";
    import type { Scheduler } from "../lib/proto";
    import { buildNextLearnMsg } from "./lib";
    import { bridgeLink } from "../lib/bridgecommand";
    import * as tr from "../lib/ftl";

    export let info: Scheduler.CongratsInfoResponse;

    const congrats = tr.schedulingCongratulationsFinished();
    let nextLearnMsg: string;
    $: nextLearnMsg = buildNextLearnMsg(info);
    const today_reviews = tr.schedulingTodayReviewLimitReached();
    const today_new = tr.schedulingTodayNewLimitReached();

    const unburyThem = bridgeLink("unbury", tr.schedulingUnburyThem());
    const buriedMsg = tr.schedulingBuriedCardsFound({ unburyThem });
    const customStudy = bridgeLink("customStudy", tr.schedulingCustomStudy());
    const customStudyMsg = tr.schedulingHowToCustomStudy({
        customStudy,
    });
</script>

<Container --gutter-block="1rem" --gutter-inline="2px" breakpoint="sm">
    <Col --col-justify="center">
        <div class="congrats">
            <h3>{congrats}</h3>

            <p>{nextLearnMsg}</p>

            {#if info.reviewRemaining}
                <p>{today_reviews}</p>
            {/if}

            {#if info.newRemaining}
                <p>{today_new}</p>
            {/if}

            {#if info.bridgeCommandsSupported}
                {#if info.haveSchedBuried || info.haveUserBuried}
                    <p>
                        {@html buriedMsg}
                    </p>
                {/if}

                {#if !info.isFilteredDeck}
                    <p>
                        {@html customStudyMsg}
                    </p>
                {/if}
            {/if}

            {#if info.deckDescription}
                <div class="description">
                    {@html info.deckDescription}
                </div>
            {/if}
        </div>
    </Col>
</Container>

<style lang="scss">
    .congrats {
        max-width: 30em;
        font-size: var(--base-font-size);

        :global(a) {
            color: var(--link);
            text-decoration: none;
        }

        h3 {
            font-weight: bold;
        }
    }

    .description {
        border: 1px solid var(--border);
        padding: 1em;
    }
</style>

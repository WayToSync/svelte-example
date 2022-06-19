<script lang="ts">
  import { onMount } from 'svelte'
  export let recipients: string[]

  // Value displays in the badge
  let badgeValue: number = 0
  // Width of the recipients
  let widthRecip: number
  // Choose to display the badge or not
  let visibility: boolean = false

  // Predict the size of the elements using text measure
  const calcWidth = value => {
    const font = '16px Arial, sans-serif'
    const canvas = document.createElement('canvas')
    const context = canvas.getContext('2d')
    context.font = font
    const { width } = context.measureText(value)
    return width
  }

  // Use onMount function to get the cell size
  onMount(() => {
    // Measures related to the cell elements
    const cellSize: number = document.querySelectorAll('td')[1].offsetWidth
    const paddingCell: number = 20
    const borderSize: number = 2
    let spanSize: number = cellSize - paddingCell - borderSize
    let badgeSize: number = 0

    // Delete Data until it can fit in the cell
    while (
      widthRecip >= spanSize &&
      (recipients.length > 2 ||
        (visibility === false && recipients.length === 2))
    ) {
      recipients = recipients
        .slice(0, recipients.length - 1)
        .map(value => ' ' + value)
      badgeValue += 1
      visibility = true
      badgeSize = calcWidth('+' + badgeValue.toString())
    }

    // If there is a badge add ' ...' at the end of the data
    if (visibility) recipients[recipients.length] = ' ...'

    // Check if the data can fit with the badge
    if (
      calcWidth(recipients) >= spanSize - badgeSize &&
      recipients.length > 1
    ) {
      recipients.splice(recipients.length - 2, recipients.length - 2)
    }
  })
</script>

<style lang="scss">
  div {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .recip {
    display: inline-block;
    overflow: hidden;
    text-overflow: ellipsis;
    color: #333333;
  }
  .badge {
    display: inline-block;
    color: #f0f0f0;
    background-color: #666666;
    border-radius: 3px;
    padding: 2px 5px;
  }
</style>

<!--
@component
Receives a `recipients` string prop and renders intelligently email recipients

- Usage:
  ```tsx
  <RecipientsDisplay {recipients} />
  ```
-->
<div>
  <span class="recip" bind:clientWidth={widthRecip}>{recipients}</span>
  {#if visibility}
    <span class="badge">{`+${badgeValue}`}</span>
  {/if}
</div>

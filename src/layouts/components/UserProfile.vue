<script setup>
import userImg from '@images/angel-rivera.jpg'
</script>

<template>
  <VBadge
    dot
    location="bottom right"
    offset-x="3"
    offset-y="3"
    bordered
    color="success"
  >
    <VAvatar
      class="cursor-pointer"
    >
      <VImg :src="userImg" />

      <!-- SECTION Menu -->
      <VMenu
        activator="parent"
        width="300"
        location="bottom end"
        offset="14px"
      >
        <VList>
          <!-- 👉 User Avatar & Name -->
          <VListItem>
            <template #prepend>
              <VListItemAction start>
                  <VAvatar
                  >
                    <VImg :src="userImg" />
                  </VAvatar>
              </VListItemAction>
            </template>

            <VListItemTitle class="font-weight-semibold">
              Angel Rivera
            </VListItemTitle>
            <VListItemSubtitle>Web Developer</VListItemSubtitle>
            <VListItemSubtitle>rivera.angelsummer@gmail.com</VListItemSubtitle>
          </VListItem>
        </VList>
      </VMenu>
      <!-- !SECTION -->
    </VAvatar>
  </VBadge>
</template>

<template>
  <div>
    <UiPageHeader
      :path="[{ label: 'ニュース', to: '/news' }]"
      :subject="news.details.group_nm"
      subheading="News Release"
    />

    <div class="l-container--col-2 l-container--contents">
      <div class="l-container--col-2__main">
        <article class="c-article">
          <header>
            <h1 class="c-heading--lv1">
              {{ news.details.subject }}
            </h1>
            <time class="c-topics__date" :datetime="news.details.ymd">{{
              news.details.ymd
            }}</time>
            <span class="c-badge">
              {{ news.details.contents_type_nm }}
            </span>
          </header>
          <div class="l-container--contents">
            <div v-html="news.details.contents"></div>
          </div>

          <hr />
          <div class="l-container--contents u-pt-30 u-text-align-center">
            <NuxtLink :to="'/news/'" class="c-button">
              ニュースリリース一覧へ戻る
            </NuxtLink>
          </div>
        </article>
      </div>
      <ContentSideBar :conditions="newsConditionMaster?.list" />
    </div>
  </div>
</template>

<script setup>
const config = useRuntimeConfig();
const route = useRoute();

const { data: news } = await useFetch(
  `${config.public.kurocoApiDomain}/rcms-api/1/news/details/${route.params.id}`,
  {
    credentials: 'include',
  }
);
const { data: newsConditionMaster } = await useFetch(
  `${config.public.kurocoApiDomain}/rcms-api/1/master`,
  {
    credentials: 'include',
  }
);

// メタ情報の追加（title, description, Twitterカード）
useHead(() => {
  const subject = news.value?.details.subject || 'ニュース';
  const description = news.value?.details.description || '';

  return {
    title: subject,
    meta: [
      {
        name: 'og:title',
        content: subject,
      },
      {
        name: 'og:description',
        content: description,
      },
      {
        name: 'og:image',
        content: 'https://kuroco.app/kuroco-card.png',
      },
      // Twitter card meta
      {
        name: 'twitter:card',
        content: 'summary',
      },
      {
        name: 'twitter:title',
        content: subject,
      },
      {
        name: 'twitter:description',
        content: description,
      },
    ],
  };
});
</script>

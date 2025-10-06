<template>
    <div v-if="data">
        <div class="relative pt-10 pb-8 md:py-[72px] lg:pb-[104px]">
            <div class="container">
                <div class="md:mb-20 lg:mb-[128px]">
                    <div
                        class="flex flex-col items-center mb-8 md:mb-14 lg:mb-20"
                    >
                        <div
                            class="text-sm leading-none tracking-[-0.41px] text-appGray-600 mb-2.5 md:text-base md:leading-none lg:text-xl lg:leading-none lg:mb-5"
                        >
                            {{ dateUtil.format(data.meta.date.timestamp) }}
                        </div>
                        <h1
                            class="leading-none font-medium tracking-[-0.41px] md:text-2xl md:leading-none lg:text-[56px] lg:leading-none"
                        >
                            {{ data.meta.title }}
                        </h1>
                    </div>
                    <BCMSImage
                        :media="data.meta.cover_image"
                        class="aspect-[2.07] rounded-lg overflow-hidden w-full cover mb-6 md:mb-8 lg:aspect-[2.43] lg:rounded-2xl lg:mb-12"
                        :client="data.bcms"
                    />
                    <ContentManager :items="data.content" class="prose" />
                </div>
                <div class="max-md:hidden">
                    <div
                        class="flex items-center justify-between mb-8 lg:mb-12"
                    >
                        <h2
                            class="text-lg leading-none font-medium tracking-[-0.41px] lg:text-[32px] lg:leading-none"
                        >
                            Other posts
                        </h2>
                        <NuxtLink
                            to="/blog"
                            class="flex items-center px-[15px] py-2.5 bg-appAccent rounded-[32px]"
                        >
                            <span
                                class="leading-none tracking-[-0.41px] mr-2 lg:text-xl lg:leading-none"
                            >
                                See all posts
                            </span>
                            <SvgoArrow
                                filled
                                :font-controlled="false"
                                class="w-[14px] h-[14px] md:w-4 md:h-4 lg:w-5 lg:h-5"
                            />
                        </NuxtLink>
                    </div>
                    <div
                        class="grid grid-cols-2 auto-rows-fr gap-6 p-4 border border-appGray-300 rounded-2xl lg:grid-cols-3 xl:gap-10 xl:p-8"
                    >
                        <BlogsCard
                            v-for="(blog, index) in data.otherBlogs"
                            :key="index"
                            :blog="blog"
                            :bcms="data.bcms"
                        />
                    </div>
                </div>
            </div>
            <TopGradient />
        </div>
    </div>
</template>

<script setup lang="ts">
import { BCMSImage } from '@thebcms/components-vue';
import type { BlogResponse } from '~/server/api/blog/[slug]';

const { setOgHead } = useHeadTags();
const route = useRoute();

const { data, error } = await useFetch<BlogResponse>(
    `/api/blog/${route.params.slug}`,
);

if (!data.value || error.value) {
    throw createError({
        statusCode: 404,
        statusMessage: 'Page Not Found',
    });
}

useHead(() => ({
    title: data.value.meta.seo.title,
    meta: [
        { name: 'keywords', content: data.value.meta.seo.keywords.join(', ') },
        { property: 'og:image', content: data.value.meta.seo.og_image.url },
        { property: 'og:title', content: data.value.meta.seo.title },
        { property: 'og:description', content: data.value.meta.seo.description },
        { property: 'og:type', content: 'article' },
        { property: 'og:url', content: data.value.meta.seo.url },
        { property: 'og:site_name', content: data.value.meta.seo.site_name },
        { name: 'twitter:title', content: data.value.meta.seo.title },
        { name: 'twitter:description', content: data.value.meta.seo.description },
        { name: 'twitter:url', content: data.value.meta.seo.url },
        { name: 'twitter:image', content: data.value.meta.seo.og_image.url },
        { name: 'twitter:image:alt', content: data.value.meta.seo.og_image_alt },
        { name: 'fb:image', content: data.value.meta.seo.og_image.url },
        { name: 'fb:image:alt', content: data.value.meta.seo.og_image_alt },
        { property: 'og:image:alt', content: data.value.meta.seo.og_image_alt },
        { property: 'twitter:image', content: data.value.meta.seo.og_image.url },
        { property: 'twitter:image:alt', content: data.value.meta.seo.og_image_alt },
        { property: 'fb:image', content: data.value.meta.seo.og_image.url },
        { property: 'fb:image:alt', content: data.value.meta.seo.og_image_alt }
    ]
}));
</script>

/**
 * @file 预渲染配置
 */

'use strict';

const path = require('path');
const PrerenderSpaPlugin = require('prerender-spa-plugin');
const Renderer = PrerenderSpaPlugin.PuppeteerRenderer;
const map = require('../build/merge-router');

/**
 * 需要渲染的路径
 *
 * @type {Array}
 */
let routers = map.mergeLangRouter();

module.exports = function () {
    return new PrerenderSpaPlugin({
        staticDir: path.join(__dirname, '../dist'),
        routes: routers,
        postProcess(context) {
            // 删除非 /static/ 的 <script> 标签
            context.html = context.html.replace(/<script([^>]*)>([^<]*)<\/script>/gi, (all, attr, content) => {
                // 如果需要保留预编译代码
                if (attr.indexOf('data-prerender') > -1) {
                    return all;
                }

                // 如果是外链代码，则移除
                if (!/\s+src\=['"]?\/static\//i.test(attr)) {
                    return '';
                }

                // 如果已经有 defer
                if (attr.indexOf(' defer') > -1) {
                    return all;
                } else if (attr.indexOf(' async') > -1) {
                    return all
                        .replace('<script', '<script defer')
                        .replace(/\s+async(=['"]*)?/, '');
                }

                return all;
            });

            // 删除 <iframe> 标签
            context.html = context.html.replace(/<iframe([^>]*)>([^<]*)<\/iframe>/gi, (all, attr, context) => {
                // 如果需要保留预编译代码
                if (attr.indexOf('data-prerender') > -1) {
                    return all;
                }

                return '';
            });

            return context;
        },
        renderer: new Renderer({
            maxConcurrentRoutes: 3,
            renderAfterTime: 200000,
            headless: true,
            ignoreHTTPSErrors: true,
            renderAfterDocumentEvent: 'render-event',
            injectProperty: 'PRERENDER_INJECTED',
            inject: {
                // 预渲染标识，在前端代码中使用 window.PRERENDER_INJECTED.prerender 获取
                prerender: true,
            },
        }),
        minify: {
            removeComments: true,
            collapseWhitespace: true,
            removeAttributeQuotes: true,
            // more options:
            // https://github.com/kangax/html-minifier#options-quick-reference
        },
    });
};
/**
 * @file 预渲染配置
 */

'use strict';

const path = require('path');
const PrerenderSpaPlugin = require('prerender-spa-plugin');
const Renderer = PrerenderSpaPlugin.PuppeteerRenderer;
const map = require('../build/merge-router');

/**
 * 需要渲染的路径
 *
 * @type {Array}
 */
let routers = map.mergeLangRouter();

module.exports = function () {
    return new PrerenderSpaPlugin({
        staticDir: path.join(__dirname, '../dist'),
        routes: routers,
        postProcess(context) {
            // 删除非 /static/ 的 <script> 标签
            context.html = context.html.replace(/<script([^>]*)>([^<]*)<\/script>/gi, (all, attr, content) => {
                // 如果需要保留预编译代码
                if (attr.indexOf('data-prerender') > -1) {
                    return all;
                }

                // 如果是外链代码，则移除
                if (!/\s+src\=['"]?\/static\//i.test(attr)) {
                    return '';
                }

                // 如果已经有 defer
                if (attr.indexOf(' defer') > -1) {
                    return all;
                } else if (attr.indexOf(' async') > -1) {
                    return all
                        .replace('<script', '<script defer')
                        .replace(/\s+async(=['"]*)?/, '');
                }

                return all;
            });

            // 删除 <iframe> 标签
            context.html = context.html.replace(/<iframe([^>]*)>([^<]*)<\/iframe>/gi, (all, attr, context) => {
                // 如果需要保留预编译代码
                if (attr.indexOf('data-prerender') > -1) {
                    return all;
                }

                return '';
            });

            return context;
        },
        renderer: new Renderer({
            maxConcurrentRoutes: 3,
            renderAfterTime: 200000,
            headless: true,
            ignoreHTTPSErrors: true,
            renderAfterDocumentEvent: 'render-event',
            injectProperty: 'PRERENDER_INJECTED',
            inject: {
                // 预渲染标识，在前端代码中使用 window.PRERENDER_INJECTED.prerender 获取
                prerender: true,
            },
        }),
        minify: {
            removeComments: true,
            collapseWhitespace: true,
            removeAttributeQuotes: true,
            // more options:
            // https://github.com/kangax/html-minifier#options-quick-reference
        },
    });
};

---
title: useOne
siderbar_label: useOne
source: packages/core/src/hooks/data/useOne.ts
---

import BasicUsageLivePreview from "./basic-usage-live-preview.md";

`useOne` is an extended version of TanStack Query's [`useQuery`](https://tanstack.com/query/v4/docs/react/reference/useQuery) that supports all of its features and adds some more.

-   It uses the `getOne` method as the **query function** from the [`dataProvider`](/api-reference/core/providers/data-provider.md) which is passed to `<Refine>`.

-   It uses a query key to cache the data. The **query key** is generated from the provided properties. You can see the query key by using the TanStack Query devtools.

It is useful when you want to fetch a single record from the API. It will return the data and some functions to control the query.

## Basic Usage

The `useOne` hook expects a `resource` and `id` property, which will be passed to the `getOne` method from the `dataProvider` as a parameter.

When these properties are changed, the `useOne` hook will trigger a new request.

<BasicUsageLivePreview />

## Realtime Updates

:::caution
This feature is only available if you use a [Live Provider](/docs/api-reference/core/providers/live-provider).
:::

When the `useOne` hook is mounted, it will call the `subscribe` method from the `liveProvider` with some parameters such as `channel`, `resource` etc. It is useful when you want to subscribe to live updates.

> For more information, refer to the [`liveProvider` documentation&#8594](/docs/api-reference/core/providers/live-provider)

## Properties

### `resource` <PropTag required />

This parameter will be passed to the `getOne` method from the `dataProvider` as a parameter. t is usually used as an API endpoint path but it all depends on how you handle the `resource` in the `getOne` method.

```tsx
useOne({
    resource: "categories",
});
```

> For more information, refer to the [creating a data provider tutorial &#8594](/docs/tutorial/understanding-dataprovider/create-dataprovider/)

### `id` <PropTag required />

This prop will be passed to the `getMany` method from the `dataProvider` as a parameter. It is used to determine which records to fetch.

```tsx
useOne({
    id: 123,
});
```

### `dataProviderName`

This prop allows you to specify which `dataProvider` if you have more than one. Just pass it like in the example:

```tsx
useOne({
    dataProviderName: "second-data-provider",
});
```

### `queryOptions`

`queryOptions` is used to pass additional options to the `useQuery` hook. It is useful when you want to pass additional options to the `useQuery` hook.

```tsx
useOne({
    queryOptions: {
        retry: 3,
        enabled: false,
    },
});
```

> For more information, refer to the [`useQuery` documentation&#8594](https://tanstack.com/query/v4/docs/react/reference/useQuery)

### `meta`

`meta` is a special property that can be used to pass additional information to data provider methods for the following purposes:

-   Customizing the data provider methods for specific use cases.
-   Generating GraphQL queries using plain JavaScript Objects (JSON).

In the following example, we pass the `headers` property in the `meta` object to the `create` method. With similar logic, you can pass any properties to specifically handle the data provider methods.

```tsx
useOne({
    // highlight-start
    meta: {
        headers: { "x-meta-data": "true" },
    },
    // highlight-end
});

const myDataProvider = {
    //...
    getOne: async ({
        resource,
        id,
        // highlight-next-line
        meta,
    }) => {
        // highlight-next-line
        const headers = meta?.headers ?? {};
        const url = `${apiUrl}/${resource}/${id}`;

        //...
        //...

        // highlight-next-line
        const { data } = await httpClient.get(`${url}`, { headers });

        return {
            data,
        };
    },
    //...
};
```

> For more information, refer to the [`meta` section of the General Concepts documentation&#8594](/docs/api-reference/general-concepts/#meta)

### `successNotification`

:::caution
[`NotificationProvider`](/docs/api-reference/core/providers/notification-provider/) is required for this prop to work.
:::

After data is fetched successfully, `useOne` can call `open` function from `NotificationProvider` to show a success notification. With this prop, you can customize the success notification.

```tsx
useOne({
    successNotification: (data, values, resource) => {
        return {
            message: `${data.title} Successfully fetched.`,
            description: "Success with no errors",
            type: "success",
        };
    },
});
```

### `errorNotification`

:::caution
[`NotificationProvider`](/docs/api-reference/core/providers/notification-provider/) is required for this prop to work.
:::

After data fetching is failed, `useOne` will call the `open` function from `NotificationProvider` to show an error notification. With this prop, you can customize the error notification.

```tsx
useOne({
    errorNotification: (data, values, resource) => {
        return {
            message: `Something went wrong when getting ${data.id}`,
            description: "Error",
            type: "error",
        };
    },
});
```

### `liveMode`

:::caution
[`LiveProvider`](/docs/api-reference/core/providers/live-provider/) is required for this prop to work.
:::

Determines whether to update data automatically ("auto") or not ("manual") if a related live event is received. It can be used to update and show data in Realtime throughout your app.
For more information about live mode, please check the [Live / Realtime](/docs/api-reference/core/providers/live-provider/#livemode) page.

```tsx
useOne({
    liveMode: "auto",
});
```

> For more information, refer to the [Live / Realtime page&#8594](/docs/api-reference/core/providers/live-provider/#livemode)

### `onLiveEvent`

:::caution
[`LiveProvider`](/docs/api-reference/core/providers/live-provider/) is required for this prop to work.
:::

The callback function is executed when new events from a subscription have arrived.

```tsx
useOne({
    onLiveEvent: (event) => {
        console.log(event);
    },
});
```

### `liveParams`

:::caution
[`LiveProvider`](/docs/api-reference/core/providers/live-provider/) is required for this prop to work.
:::

Params to pass to liveProvider's [subscribe](/docs/api-reference/core/providers/live-provider/#subscribe) method.

## Return Values

Returns an object with TanStack Query's `useQuery` return values.

> For more information, refer to the [ `useQuery` documentation &#8594](https://tanstack.com/query/v4/docs/react/reference/useQuery)

## API

### Properties

<PropsTable module="@refinedev/core/useOne"
successNotification-default='`false`'
errorNotification-default='"Error (status code: `statusCode`)"'
/>

### Type Parameters

| Property     | Desription                                                                                                                                                          | Type                       | Default                    |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | -------------------------- |
| TQueryFnData | Result data returned by the query function. Extends [`BaseRecord`][baserecord]                                                                                      | [`BaseRecord`][baserecord] | [`BaseRecord`][baserecord] |
| TError       | Custom error object that extends [`HttpError`][httperror]                                                                                                           | [`HttpError`][httperror]   | [`HttpError`][httperror]   |
| TData        | Result data returned by the `select` function. Extends [`BaseRecord`][baserecord]. If not specified, the value of `TQueryFnData` will be used as the default value. | [`BaseRecord`][baserecord] | `TQueryFnData`             |

### Return values

| Description                               | Type                                                                                                   |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Result of the TanStack Query's `useQuery` | [`QueryObserverResult<{ data: TData; }>`](https://tanstack.com/query/v4/docs/react/reference/useQuery) |

[baserecord]: /api-reference/core/interfaces.md#baserecord
[httperror]: /api-reference/core/interfaces.md#httperror

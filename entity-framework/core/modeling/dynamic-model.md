---
title: "使用相同的 DbContext 类型的 EF 核心的多个模型之间交替"
author: AndriySvyryd
uid: core/modeling/dynamic-model
ms.openlocfilehash: e6828c62c55ae6f48d46a20528268264c3f22b26
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="7aa7d-102">使用相同的 DbContext 类型的多个模型之间交替</span><span class="sxs-lookup"><span data-stu-id="7aa7d-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="7aa7d-103">生成的模型`OnModelCreating`无法使用属性在上下文更改生成模型的方式。</span><span class="sxs-lookup"><span data-stu-id="7aa7d-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="7aa7d-104">例如它无法用于排除某些属性：</span><span class="sxs-lookup"><span data-stu-id="7aa7d-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="7aa7d-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="7aa7d-105">IModelCacheKeyFactory</span></span>
<span data-ttu-id="7aa7d-106">但是如果你尝试执行上述无需进行其他更改则会得到相同的模型每次创建新的上下文时的任何值的`IgnoreIntProperty`。</span><span class="sxs-lookup"><span data-stu-id="7aa7d-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="7aa7d-107">这引起缓存 EF 使用通过仅调用来提高性能的机制的模型`OnModelCreating`一次，并且缓存模型。</span><span class="sxs-lookup"><span data-stu-id="7aa7d-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="7aa7d-108">默认情况下 EF 假定，对于任何给定的上下文类型模型处于相同。</span><span class="sxs-lookup"><span data-stu-id="7aa7d-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="7aa7d-109">若要实现此目的的默认实现`IModelCacheKeyFactory`返回上下文类型中只包含一个键。</span><span class="sxs-lookup"><span data-stu-id="7aa7d-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="7aa7d-110">若要更改此你需要更换`IModelCacheKeyFactory`服务。</span><span class="sxs-lookup"><span data-stu-id="7aa7d-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="7aa7d-111">需要对使用其他模型项返回可比较的对象的新实现`Equals`考虑会影响模型的所有变量的方法：</span><span class="sxs-lookup"><span data-stu-id="7aa7d-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
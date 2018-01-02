---
title: "为生成的属性的 EF 核心设置显式值"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3f1993c2-cdf5-425b-bac2-a2665a20322b
ms.technology: entity-framework-core
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: f34e92d9a3b10b6ff904257ccd047a8acdaad231
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="setting-explicit-values-for-generated-properties"></a><span data-ttu-id="b4ff1-102">设置生成的属性的显式值</span><span class="sxs-lookup"><span data-stu-id="b4ff1-102">Setting Explicit Values for Generated Properties</span></span>

<span data-ttu-id="b4ff1-103">生成的属性是的属性，其值生成的 （无论是 EF 或数据库） 时该实体是添加和/或更新。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-103">A generated property is a property whose value is generated (either by EF or the database) when the entity is added and/or updated.</span></span> <span data-ttu-id="b4ff1-104">请参阅[生成属性](../modeling/generated-properties.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-104">See [Generated Properties](../modeling/generated-properties.md) for more information.</span></span>

<span data-ttu-id="b4ff1-105">可能的情况下你想要设置为生成的属性，而不是让一个生成一个显式值。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-105">There may be situations where you want to set an explicit value for a generated property, rather than having one generated.</span></span>

> [!TIP]  
> <span data-ttu-id="b4ff1-106">你可以查看这篇文章[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/ExplicitValuesGenerateProperties/)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/ExplicitValuesGenerateProperties/) on GitHub.</span></span>

## <a name="the-model"></a><span data-ttu-id="b4ff1-107">模型</span><span class="sxs-lookup"><span data-stu-id="b4ff1-107">The model</span></span>

<span data-ttu-id="b4ff1-108">在本文中使用的模型包含单个`Employee`实体。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-108">The model used in this article contains a single `Employee` entity.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a><span data-ttu-id="b4ff1-109">在添加过程中保存一个显式值</span><span class="sxs-lookup"><span data-stu-id="b4ff1-109">Saving an explicit value during add</span></span>

<span data-ttu-id="b4ff1-110">`Employee.EmploymentStarted`属性被配置为具有生成的新实体 （使用默认值） 的数据库值。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-110">The `Employee.EmploymentStarted` property is configured to have values generated by the database for new entities (using a default value).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

<span data-ttu-id="b4ff1-111">下面的代码将两个员工插入到数据库。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-111">The following code inserts two employees into the database.</span></span>
* <span data-ttu-id="b4ff1-112">首先，没有值分配给`Employee.EmploymentStarted`属性，使它保持设置的 CLR 默认值为`DateTime`。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-112">For the first, no value is assigned to `Employee.EmploymentStarted` property, so it remains set to the CLR default value for `DateTime`.</span></span>
* <span data-ttu-id="b4ff1-113">对于第二个，我们设置的显式值的`1-Jan-2000`。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-113">For the second, we have set an explicit value of `1-Jan-2000`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

<span data-ttu-id="b4ff1-114">输出显示该数据库为第一个员工生成一个值，并且我们显式值用于第二个。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-114">Output shows that the database generated a value for the first employee and our explicit value was used for the second.</span></span>

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a><span data-ttu-id="b4ff1-115">到 SQL Server 标识列的显式值</span><span class="sxs-lookup"><span data-stu-id="b4ff1-115">Explicit values into SQL Server IDENTITY columns</span></span>

<span data-ttu-id="b4ff1-116">按照约定`Employee.EmployeeId`属性是存储生成`IDENTITY`列。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-116">By convention the `Employee.EmployeeId` property is a store generated `IDENTITY` column.</span></span>

<span data-ttu-id="b4ff1-117">大多数情况下，如上所示的方法将适用于键属性。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-117">For most situations, the approach shown above will work for key properties.</span></span> <span data-ttu-id="b4ff1-118">但是，若要将显式值插入到 SQL Server`IDENTITY`列中，你需要手动启用`IDENTITY_INSERT`之前调用`SaveChanges()`。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-118">However, to insert explicit values into a SQL Server `IDENTITY` column, you need to manually enable `IDENTITY_INSERT` before calling `SaveChanges()`.</span></span>

> [!NOTE]  
> <span data-ttu-id="b4ff1-119">我们有[功能请求](https://github.com/aspnet/EntityFramework/issues/703)在我们的积压工作来自动执行此操作的 SQL Server 提供程序内。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-119">We have a [feature request](https://github.com/aspnet/EntityFramework/issues/703) on our backlog to do this automatically within the SQL Server provider.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

<span data-ttu-id="b4ff1-120">输出显示提供的 id 已保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-120">Output shows that the supplied ids were saved to the database.</span></span>

``` Console
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a><span data-ttu-id="b4ff1-121">在更新过程中设置一个显式值</span><span class="sxs-lookup"><span data-stu-id="b4ff1-121">Setting an explicit value during update</span></span>

<span data-ttu-id="b4ff1-122">`Employee.LastPayRaise`属性配置为具有在更新过程中由数据库生成的值。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-122">The `Employee.LastPayRaise` property is configured to have values generated by the database during updates.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]  
> <span data-ttu-id="b4ff1-123">默认情况下，EF 核心将引发异常，如果你尝试保存已显式配置为在更新过程中生成属性的值。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-123">By default, EF Core will throw an exception if you try to save an explicit value for a property that is configured to be generated during update.</span></span> <span data-ttu-id="b4ff1-124">若要避免此问题，你需要到较低级别的元数据 API 下拉列表中，设置`AfterSaveBehavior`（如上所示）。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-124">To avoid this, you need to drop down to the lower level metadata API and set the `AfterSaveBehavior` (as shown above).</span></span>

> [!NOTE]  
> <span data-ttu-id="b4ff1-125">**EF 核心 2.0 中的更改：**通过控制后保存行为已在以前的版本`IsReadOnlyAfterSave`标志。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-125">**Changes in EF Core 2.0:** In previous releases the after-save behavior was controlled through the `IsReadOnlyAfterSave` flag.</span></span> <span data-ttu-id="b4ff1-126">过时此标志和将其替换为`AfterSaveBehavior`。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-126">This flag has been obsoleted and replaced by `AfterSaveBehavior`.</span></span>

<span data-ttu-id="b4ff1-127">要生成的值的数据库中还有触发器`LastPayRaise`期间的列`UPDATE`操作。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-127">There is also a trigger in the database to generate values for the `LastPayRaise` column during `UPDATE` operations.</span></span>

[!code-sql[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

<span data-ttu-id="b4ff1-128">下面的代码会增加数据库中的两个雇员的薪水。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-128">The following code increases the salary of two employees in the database.</span></span>
* <span data-ttu-id="b4ff1-129">首先，没有值分配给`Employee.LastPayRaise`属性，因此它将保留为 null。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-129">For the first, no value is assigned to `Employee.LastPayRaise` property, so it remains set to null.</span></span>
* <span data-ttu-id="b4ff1-130">对于第二个，我们设置的显式值的前一周 （后可以追溯支付引发）。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-130">For the second, we have set an explicit value of one week ago (back dating the pay raise).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

<span data-ttu-id="b4ff1-131">输出显示该数据库为第一个员工生成一个值，并且我们显式值用于第二个。</span><span class="sxs-lookup"><span data-stu-id="b4ff1-131">Output shows that the database generated a value for the first employee and our explicit value was used for the second.</span></span>

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
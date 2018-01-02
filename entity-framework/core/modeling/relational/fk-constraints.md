---
title: "外键约束的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
ms.technology: entity-framework-core
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: 726f03e2ee4cd3ec851c9a861b75dd12f9203e9c
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="38532-102">外键约束</span><span class="sxs-lookup"><span data-stu-id="38532-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="38532-103">一般情况下，此部分中的配置是适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="38532-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="38532-104">此处所示的扩展方法将变为可用时安装关系数据库提供程序 (由于共享*Microsoft.EntityFrameworkCore.Relational*包)。</span><span class="sxs-lookup"><span data-stu-id="38532-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="38532-105">外键约束是为每个关系在模型中引入的。</span><span class="sxs-lookup"><span data-stu-id="38532-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="38532-106">约定</span><span class="sxs-lookup"><span data-stu-id="38532-106">Conventions</span></span>

<span data-ttu-id="38532-107">按照约定，名为外键约束`FK_<dependent type name>_<principal type name>_<foreign key property name>`。</span><span class="sxs-lookup"><span data-stu-id="38532-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="38532-108">复合外键`<foreign key property name>`将成为外键属性名称下划线分隔列表。</span><span class="sxs-lookup"><span data-stu-id="38532-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="38532-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="38532-109">Data Annotations</span></span>

<span data-ttu-id="38532-110">外键约束名称不能使用数据注释配置。</span><span class="sxs-lookup"><span data-stu-id="38532-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="38532-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="38532-111">Fluent API</span></span>

<span data-ttu-id="38532-112">可以使用 Fluent API 来配置关系的外键约束名称。</span><span class="sxs-lookup"><span data-stu-id="38532-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/RelationshipConstraintName.cs?highlight=12)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .HasForeignKey(p => p.BlogId)
            .HasConstraintName("ForeignKey_Post_Blog");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```
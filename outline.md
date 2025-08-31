"Plain Language Law - Building a Sustainable AI-Powered Legal Translation Service"

## Talk Structure (20 minutes)

### 1. Opening Hook (2 minutes) - "The Opportunity"
**Title Slide:** "What if understanding the law didn't require a law degree?"

- **The Democratic Crisis**: Everyone in New Zealand must follow the law, but few can understand it
- **Real Impact**: Show a snippet of original legislation vs. plain language version
- **The Scale Challenge**: 100+ acts, thousands of provisions, constant updates
- **The Question**: How do you make legal text accessible without breaking the bank or creating a maintenance nightmare?

### 2. The Constraints (3 minutes) - "Working Within Reality"

**The Constraints That Shaped Everything:**
- **Low-cost**: No venture capital, no massive team
- **Low-maintenance**: Can't become a full-time job
- **Longevity**: Must survive without constant developer attention
- **Quality**: Can't compromise accuracy for convenience

### 3. The Technical Architecture (5 minutes) - "AI Engineering at Scale"

#### **The Content Pipeline:**
1. **Source**: Official New Zealand legislation XML (copyright-free!)
2. **Processing**: Ruby-based collation system breaks XML into digestible chunks
3. **AI Translation**: Claude 3.5 Sonnet via AWS Bedrock
4. **Publishing**: Static site generation with Astro

#### **Smart Prompt Engineering:**
- **Context-Aware Prompts**: Different prompts for acts, provisions, schedules
- **Consistent Voice**: "Elementary school student who wants to learn about the law"
- **Safety Rails**: "Do not infer meaning beyond the exact text provided"
- **New Zealand Specific**: "Use New Zealand spelling, for example 'summarise' NOT 'summarize'"

#### **The AI Integration Strategy:**
```ruby
# Clean, versioned prompts
SYSTEM_PROMPT = "Convert this New Zealand legislation into plain language..."

# Metadata tracking for optimization
plain_language_metadata:
  model: us.anthropic.claude-3-5-sonnet-20240620-v1:0
  prompt_id: ackama.plainlanguageservice.plainlanguage-20241101:v1
  usage: { inputTokens: 952, outputTokens: 161 }
```

### 4. Cost Engineering & Automation (4 minutes) - "The Sustainability Secret"

#### **Low-Cost Infrastructure:**
- **Hosting**: Static site on AWS S3 + CloudFront (~$10/month)
- **AI Costs**: AWS Bedrock (pay-per-token, not per-API-call)
- **Automation**: GitHub Actions handles everything
- **No Servers**: Zero ongoing infrastructure management

#### **Smart Automation:**
- **Daily RSS Monitoring**: Automatically detects new legislation
- **Selective Processing**: Only processes genuinely new/changed content
- **Batch Operations**: Groups AI calls for efficiency
- **Performance Optimization**: PurgeCSS reduces bundle size by 60%+

Journey from Claude to Llama
How things progressed

### 5. The Human-AI Collaboration (3 minutes) - "What AI Can and Can't Do"

#### **AI Excels At:**
- **Consistent Tone**: Never gets tired, always follows style guide
- **Scale**: Processes thousands of provisions without complaint
- **Updates**: Re-processes changes in hours, not months
- **Context Switching**: Handles tax law, criminal law, housing law equally well

#### **Human Oversight For:**
- **Quality Control**: Spot-checking outputs
- **Edge Cases**: Complex schedules, tables, special formatting
- **Taxonomy**: Organizing content into user-friendly categories
- **Strategic Decisions**: Which acts to prioritize, how to structure content

#### **Transparency & Trust:**
- Open about AI limitations on the site
- Links to original legislation always provided
- Clear disclaimers about accuracy
- Community feedback encouraged

### 6. Real-World Impact & Lessons (2 minutes) - "What We've Learned"

#### **Metrics That Matter:**
- **Content Coverage**: 100+ acts translated
- **User Experience**: From legal jargon to 12-year-old reading level
- **Maintenance Time**: ~2 hours/month after initial setup
- **Cost**: <$100/month total operational cost

#### **Key Engineering Lessons:**
1. **Constraints Drive Innovation**: Limited budget forced clever architecture
2. **Automation Is Essential**: Manual processes don't scale
3. **Static Sites Win**: For content-heavy sites, going static = going sustainable
4. **Prompt Versioning**: Track prompts like code for reproducibility
5. **Fail Fast, Fail Cheap**: Better to miss an edge case than over-engineer

### 7. The Future & Call to Action (1 minute) - "Beyond New Zealand"

#### **What's Next:**
- **Multi-language Support**: Māori, Samoan translations in progress
- **Embeddings of summaries**
- **More value-adds to content - topics, categories**
- **Advanced Features**: Enhanced search, better categorization
- **Open Source Potential**: Framework could work for other jurisdictions

#### **For AI Engineers:**
- **The Opportunity**: Government information is often copyright-free and ready for AI translation
- **The Challenge**: Build systems that last without constant maintenance
- **The Impact**: Somethign else

---

## Key Talking Points to Emphasize:

1. **Constraint-Driven Design**: How limitations led to better architecture
2. **Practical AI Engineering**: Real-world prompt engineering and cost management
3. **Sustainability Focus**: Building for longevity, not just launch
4. **Boring technology**: Using well established solid tools to reduce work for AI.
5. **Honest AI**: Transparent about limitations and biases

## Demo Suggestions:
- Show before/after of complex legal text
- Walk through the automated processing pipeline
- Display the cost dashboard (if available)
- Show the taxonomy organization system

This outline tells the story of innovation within constraints while providing concrete technical details that will resonate with an AI engineering audience. It balances the inspirational aspects of the project with the practical engineering decisions that made it sustainable.


## Process

Talk about process + boring tech:

- Collation (XML -> HTML files w. frontmatter, YAML structure)
- Filesystem (Move files into site folder structure)
- Content (XML -> HTML) via XSLT
- Plain language (AI -> HTML) via Claude, now Llama
- Featured provisions (Structure -> AI -> Frontmatter) via Claude
- Topic/category classification (Taxonomy + Content -> AI -> Frontmatter) via Claude
- Astro site build
- Pagefind search index build
- RSS monitoring and automating pipeline via Github Actions
- Deploy to S3 w. Cloudfront

## Challenges

- Jekyll build times - tried 11ty, Next.js, Bridgetown - surprising number struggle just fitting things in memory
- Astro needed some custom loading code for large numbers of files, but offers flexibility - island architecture, typescript support.
- Could get rid of some Jekyll plugins added to make it faster to build.
- Balancing translation quality and cost - Claude is good, but Llama is cheaper.
- QA opportunities - would like to support multilingual plain language, prototyped it, but unclear how we can QA in a sustainable way.

## Opportunities

- About to move to embeddings generated for content, which will allow us to do more with the content.
- We have trialled improving Llama summarisation by providing examples of 'good summarisation' from Claude, but need examples that a conceptually similar - vector search allows for this.
- Have tried various ways to start building in more content around topics and categories - we're expecting to use embeddings to drive this as well - FAQ extraction, potentially some more structured content and hierarchy within topics and categories.
